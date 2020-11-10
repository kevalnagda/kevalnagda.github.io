---
layout: single
title: "Conda in Dockerfile"
date: 2020-11-10 12:00:00 +0530
categories: jekyll update
---
Conda is an open-source package manager that helps you quickly install, run and update packages and their dependencies. It helps you easily create, save, load and switch between different environments on your system.
Dockerfile is a text file that defines a set of commands or operations which aid you to build your own custom Docker image. And in order to build a Conda-based application, you'll need to activate a Conda environment in the Dockerfile.
 
Unfortunately, the approach of activating Conda environments in a Dockerfile is a bit different than you would expect.
 
## Defining the Conda environment
Firstly, let's create an `environment.yml` file to define the Conda environment.
 
```
name: env
channels:
   - conda-forge
dependencies:
   - python=3.6
   - flask
```
 
Here `env` is the name of our Conda environment. We use `conda-forge` channel to utilize Conda package provided by the conda-forge community and install two dependencies: python and flask.
 
`environment.yml` is similar to the `requirements.txt` used by virtual environment `venv` module.
 
## A simple Python program
 
Write a simple Python program to test the Conda environment activation.
 
```
import flask
 
print("Flask import successful!")
```
 
## First attempt
 
Following the standard approach, our first iteration of the Dockerfile looks like:
 
```
# Define base image
FROM continuumio/miniconda3
 
# Set working directory for the project
WORKDIR /app
 
# Create Conda environment from the YAML file
COPY environment.yml .
RUN conda env create -f environment.yml
 
# Activate Conda environment and check if it is working properly
RUN conda activate env
RUN echo "Making sure flask is installed correctly..."
RUN python -c "import flask"
 
# Python program to run in the container
COPY run.py .
ENTRYPOINT ["python", "run.py"]
```
 
Following is the result when we try building the Docker image
 
```
Step 5/9 : RUN conda activate env
---> Running in e33a2dcd4d99
 
CommandNotFoundError: Your shell has not been properly configured to use 'conda activate'.
To initialize your shell, run
 
   $ conda init <SHELL_NAME>
 
The command '/bin/sh -c conda activate env' returned a non-zero code: 1
```
 
We notice that we can't just emulate the Conda environment and we need to use Conda's own activation method.
 
## Second attempt
 
We can use `conda init bash` as a solution to the above problem. Docker by default uses `/bin/sh -c <command>` to execute instructions but we require a bash shell to run the `conda init bash` command.
 
To tackle this, we override the default shell by adding the following to the Dockerfile:
 
```
SHELL ["/bin/bash", "--login", "-c"]
```
 
Our updated Dockerfile should look as follows:
 
```
# Define base image
FROM continuumio/miniconda3
 
# Set working directory for the project
WORKDIR /app
 
# Override default shell and use bash
SHELL ["/bin/bash", "--login", "-c"]
 
# Create Conda environment from the YAML file
COPY environment.yml .
RUN conda env create -f environment.yml
 
# Initialize conda in the bash config files
RUN conda init bash
 
# Activate Conda environment and check if it is working properly
RUN conda activate env
RUN echo "Making sure flask is installed correctly..."
RUN python -c "import flask"
 
# Python program to run in the container
COPY run.py .
ENTRYPOINT ["python", "run.py"]
```
 
Building the Docker image again gives us the following result:
 
```
Step 9/11 : RUN python -c "import flask"
---> Running in ea831eb42ff6
Traceback (most recent call last):
 File "<string>", line 1, in <module>
ModuleNotFoundError: No module named 'flask'
The command '/bin/bash --login -c python -c "import flask"' returned a non-zero code: 1
```
 
So the problem is that each `RUN` instruction in a Dockerfile executes in a separate run of bash. Thus, in the above example Conda environment is activated in the first `RUN` and later `RUN`s are new shell sessions without Conda activation.
 
## Third attempt
 
Now, since each `RUN` instruction is a separate run of bash, adding `conda activate` command to the `~/.bashrc` of the current user should work. It will work perfectly fine and you will be able to build a Docker image, however, when you run a container based on that image, it will result in the same error as above.
 
This is due to the fact that we are using *exec* form of `ENTRYPOINT` instruction which doesn't actually start a shell session. An alternative to this is the *shell* form of `ENTRYPOINT` i.e. `ENTRYPOINT python run.py` but this won't work since it breaks down the container.
 
The final resort to this problem is by using `conda run -n env <command>` instruction which actually runs `<command>` inside the conda environment.
 
So the final working Dockerfile should look like:
 
```
# Define base image
FROM continuumio/miniconda3
 
# Set working directory for the project
WORKDIR /app
 
# Create Conda environment from the YAML file
COPY environment.yml .
RUN conda env create -f environment.yml
 
# Override default shell and use bash
SHELL ["conda", "run", "-n", "env", "/bin/bash", "-c"]
 
# Activate Conda environment and check if it is working properly
RUN echo "Making sure flask is installed correctly..."
RUN python -c "import flask"
 
# Python program to run in the container
COPY run.py .
ENTRYPOINT ["conda", "run", "-n", "env", "python", "run.py"]
```
 
And the result obtained is as follows:
 
```
$ docker build .
Sending build context to Docker daemon  4.608kB
Step 1/9 : FROM continuumio/miniconda3
---> b4adc22212f1
Step 2/9 : WORKDIR /app
---> Using cache
---> 658c526932d9
Step 3/9 : COPY environment.yml .
---> 26ff11b33587
Step 4/9 : RUN conda env create -f environment.yml
---> Running in d6729c106f2b
Collecting package metadata (repodata.json): ...working... done
Solving environment: ...working... done
 
Downloading and Extracting Packages
_libgcc_mutex-0.1    | 3 KB      | ########## | 100%
flask-1.1.2          | 70 KB     | ########## | 100%
zlib-1.2.11          | 106 KB    | ########## | 100%
wheel-0.35.1         | 29 KB     | ########## | 100%
ncurses-6.2          | 1022 KB   | ########## | 100%
werkzeug-1.0.1       | 239 KB    | ########## | 100%
ld_impl_linux-64-2.3 | 617 KB    | ########## | 100%
click-7.1.2          | 64 KB     | ########## | 100%
markupsafe-1.1.1     | 27 KB     | ########## | 100%
libffi-3.2.1         | 47 KB     | ########## | 100%
openssl-1.1.1h       | 2.1 MB    | ########## | 100%
python-3.6.11        | 34.2 MB   | ########## | 100%
itsdangerous-1.1.0   | 16 KB     | ########## | 100%
libstdcxx-ng-9.3.0   | 4.0 MB    | ########## | 100%
xz-5.2.5             | 343 KB    | ########## | 100%
libgcc-ng-9.3.0      | 7.8 MB    | ########## | 100%
setuptools-49.6.0    | 947 KB    | ########## | 100%
jinja2-2.11.2        | 93 KB     | ########## | 100%
ca-certificates-2020 | 145 KB    | ########## | 100%
certifi-2020.6.20    | 151 KB    | ########## | 100%
pip-20.2.4           | 1.1 MB    | ########## | 100%
libgomp-9.3.0        | 378 KB    | ########## | 100%
tk-8.6.10            | 3.2 MB    | ########## | 100%
python_abi-3.6       | 4 KB      | ########## | 100%
_openmp_mutex-4.5    | 22 KB     | ########## | 100%
sqlite-3.33.0        | 1.4 MB    | ########## | 100%
readline-8.0         | 281 KB    | ########## | 100%
Preparing transaction: ...working... done
Verifying transaction: ...working... done
Executing transaction: ...working... done
#
# To activate this environment, use
#
#     $ conda activate env
#
# To deactivate an active environment, use
#
#     $ conda deactivate
 
==> WARNING: A newer version of conda exists. <==
 current version: 4.8.2
 latest version: 4.9.1
 
Please update conda by running
 
   $ conda update -n base -c defaults conda
 
Removing intermediate container d6729c106f2b
---> c03cbd024136
Step 5/9 : SHELL ["conda", "run", "-n", "env", "/bin/bash", "-c"]
---> Running in f37ed240c11e
Removing intermediate container f37ed240c11e
---> b56ab8574a14
Step 6/9 : RUN echo "Making sure flask is installed correctly..."
---> Running in 46b28807a8e2
Making sure flask is installed correctly...
 
Removing intermediate container 46b28807a8e2
---> 70228fcf11ec
Step 7/9 : RUN python -c "import flask"
---> Running in b5a021d87998
Removing intermediate container b5a021d87998
---> 846df181bd85
Step 8/9 : COPY run.py .
---> 975a983a3504
Step 9/9 : ENTRYPOINT ["conda", "run", "-n", "env", "python", "run.py"]
---> Running in 240d3476910c
Removing intermediate container 240d3476910c
---> 72a352583f00
Successfully built 72a352583f00
 
$ docker images
REPOSITORY               TAG                 IMAGE ID            CREATED             SIZE
<none>                   <none>              72a352583f00        2 minutes ago       964MB
 
$ docker run 72a352583f00
Flask import successful!
```
 
## References

[Activating a Conda environment in your Dockerfile]

[Activating a Conda environment in your Dockerfile]: https://pythonspeed.com/articles/activate-conda-dockerfile/