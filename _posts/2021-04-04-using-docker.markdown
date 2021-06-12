---
layout: post
title:  "Setting up a scratch space using Poetry, Docker and Jupyter"
date:   2021-04-02
categories: Python Docker 
---

Often I want to experiment with a new Python package but don't want to have to set up a virtual environment, or affect the packages that I already have installed on my system. Jupyter Notebooks provide a great interface for quickly testing out new features of a library but using them inside a virtual environment can also be a pain. 

Running a Jupyter Notebook inside a Docker container is a nice solution to this problem. This post gathers together my understanding of Docker in setting this up. I'm still very much learning about Docker and haven't had many other opportunities to really see where it is essential but this is one nice use case, and a good intro for me to learn about it.

The Docker documentation has very useful and easy to follow [tutorials](https://docs.docker.com/get-started/) to begin using Docker. 

## Set up a project using Poetry
Poetry is handy for setting up the folder structure for a project, including tests, modules, a README.md and managing dependencies
```
poetry new (project_name)
```

Now enter into the Poetry shell and add the package that you're interested in testing out

```
poetry shell
poetry add (package_name)
```

And remember to add Jupyter notebook
```
poetry add jupyter
```

Finally export a `requirements.txt` file that we can use to create the Docker image.
```
pip freeze > requirements.txt
```

## Create a Dockerfile
Create a file `Dockerfile` (without any extension) in the working directory of a project. We use a Python 3.8 base image and then add the requirements.txt file with the necessary libraries.

```
FROM python:3.8-slim-buster
WORKDIR /app
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
COPY . .
CMD ["jupyter", "notebook", "--port=8888", "--no-browser", "--ip=0.0.0.0", "--allow-root"]
```

## Build the Docker image and then run it to test out the library in the notebook
In the same working directory build the image:
```
docker build --tag my_test_enviroment .
```
and run it
```
docker run -p 8888:8888 my_test_environment
```

Now you can open up a browser and create a new notebook which will have the necessary libraries from the Docker image. Because this is run inside a container, no data will be stored between stopping and running the container again so any notebooks cannot be saved. 