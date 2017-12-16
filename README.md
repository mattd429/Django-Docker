[![Docker Pulls](https://img.shields.io/docker/pulls/mashape/kong.svg)]() [![Docker Build Status](https://img.shields.io/docker/build/jrottenberg/ffmpeg.svg)]() [![PyPI](https://img.shields.io/pypi/pyversions/Django.svg)]() [![GitHub language count](https://img.shields.io/github/languages/count/badges/shields.svg)]()

![alt text](http://yacows.com.br/media/images/2013/11/12/img-0-pythonninja.png)

[Docker](https://www.docker.com/) is a containerization tool used for spinning up isolated, reproducible application enviroments. **This piece details how to containerize a Django Project, Postgres, and Redis for local development along with delivering the stack to the cloud via** [Docker Compose](https://docs.docker.com/compose/) and [Docker Machine](http://docs.docker.com/machine/)


In the end, the stack will include a separate container for each service:

- 1 web/Django container
- 1 nginx container
- 1 Postgres container
- 1 Redis container
- 1 data container

![alt text](https://realpython.com/images/blog_images/dockerizing-django/container-stack.png)

### Updates:
- *11/10/2017*: Added named data volumes to the Postgres and Redis containers.
- *11/13/2017*: Added Docker Toolbox, and also updated to the latest versions of Docker - Docker client(17.09.0-ce, build afdb6d4), Docker composer (v1.16.1, build 6d1ac21), Docker Machine(v0.12.2, build 9371605)

## Local Setup
Along with Docker(v17.09.0) we will be using -
  - [Docker Compose](v1.16.1) for orchestrating a multi-container application into a single app, and
  - [Docker Machine](v0.12.2) for creating Docker hosts both locally and in the cloud.
  
If you're running either Mac OS X or Windows, then download and install the [Docker Toolbox](https://www.docker.com/products/docker-toolbox) to get all the necessary tools. Otherwise follow the directions [here](https://docs.docker.com/compose/install/) and [here](https://docs.docker.com/machine/install-machine/) to install Docker Compose and Machine, respectively.

Once done, test out the installs:

    $ docker-machine version
    docker-machine version 0.12.2, build 9371605
    $ docker-compose version
    docker-compose version 1.16.1, build 6d1ac21
    docker-py version: 2.5.1
    CPython version: 2.7.12
    OpenSSL version: OpenSSL 1.0.2j 26 Sep 2016
    

Next clone the project from the [repository](https://github.com/mattd429/Django-Docker) or create your own project based on the project structure found on the repo:

    ├── docker-compose.yml
    ├── nginx
    │   ├── Dockerfile
    │   └── sites-enabled 
    │       └── django_project
    ├── production.yml
    └── web
        ├── Dockerfile
        ├── django_docker
        │   ├── __init__.py
        │   ├── apps
        │   │   ├── __init__.py
        │   │   └── todo
        │   │       ├── __init__.py
        │   │       ├── admin.py
        │   │       ├── models.py
        │   │       ├── templates
        │   │       │   ├── _base.html
        │   │       │   └── home.html
        │   │       ├── tests.py
        │   │       ├── urls.py
        │   │       └── views.py
        │   ├── settings.py
        │   ├── urls.py
        │   └── wsgi.py
        ├── manage.py
        ├── requirements.txt
        └── static
            └── main.css
            
        


We're now ready to get the containers up and runnning...

## Docker Machine
To start Docker Machine, simply navigate to the project root and then run:

    $ docker-mahcine create - d virtualbox dev;
    Running pre-create checks...
    Creating machine...
    (dev) Creating VirtualBox VM...
    (dev) Creating SSH key...
    (dev) Starting the VM...
    (dev) Check network to re-create if needed...
    (dev) Waiting for an IP...
    Waiting for machine to be running, this may take a few minutes...
    Detecting operating system of created instance...
    Waiting for SSH to be available...
    Detecting the provisioner...
    Provisioning with boot2docker...
    Copying certs to the local machine directory...
    Copying certs to the remote machine...
    Setting Docker configuration on the remote daemon...
    Checking connection to Docker...
    Docker is up and running!
    To see how to connect your Docker Client to the Docker Engine
    running on this virtual machine, run: docker-machine env dev
    
The `create` command set up a new "Machine" (called *dev*) for Docker development. In essence, it started a VM with the Docker client running. Now just point at the *dev* machine:

```docker
$ eval $(docker-machine env dev)
```

Run the following command to view the currently running Machines:

    $ docker-machine ls
    NAME   ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER    ERRORS
    dev    *        virtualbox   Running   tcp://192.168.99.100:2376           v1.10.3
    
