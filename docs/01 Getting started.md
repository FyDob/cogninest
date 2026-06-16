# 01 Getting Started - Installation

CogniNest is a NEST simulator module. To run it, NEST Simulator and all its pre-requisites need to be installed on your system. To achieve these pre-requisites, there are two options: either install CogniNest in a Docker container, or install NEST and CogniNest from source. In this tutorial, we will focus on the container solution, as it is the more convenient and stable installation process.

## Installing CogniNest via Docker

To quickly set up and run the NEST Simulator with the Felix Module, use the provided Docker image. Please note that you need to have Docker installed for these steps. If you don't, pleas

### Pull the Docker Image

```
docker pull maxc93/cogninest
```

### Run the Container

```
docker run --rm -it maxc93/cogninest
```

If the application runs a web service, use:

```
docker run -d -p 8080:80 maxc93/cogninest
```

Then access it at ```http://localhost:8080```.

### Syncing files between the container and the host machine

CogniNest produces both data and data visualization outputs, which you might want to further process and analyze on the host machine. To access files in the container, you can mount the container directory on the host machine.

To do so, we recommend the following process to sync files between the host machine and the container:

1) Start a container with ```docker run --name <container_name> --rm -it maxc93/cogninest```.

2) Create the directory you want to use as your synced volume on the host machine. Changes made in this directory by the container will be synced to the host, changes made on the host will be synced to the container. That means: you can program and interact with all relevant NEST files in your host environment and run NEST on the container. For the purpose of this tutorial, yours is called ```path/to/host```.

3) Copy all files you want to sync between your host machine and the container to the host directory with

```docker cp <container_name>:/app/cogninest path/to/host```

4) Stop and remove the old container with ```docker stop <container_name>``` and ```docker rm <container_name>```

5) Start a new container with the same name and the host path as a volume:

```docker run --name <container_name> --rm -it -v path/to/host/cogninest:/app/cogninest maxc93/cogninest```

Now all code, model files and plots should sync to the host machine - you can edit the code and scripts, but run the actual model in the Docker container.

## Installing CogniNest from source

Installing CogniNest/felixmodule from source enables you to modify basic model properties, such as the implementation of excitatory and inhibitory neurons, and the learning rule. However, we recommend to instead use CogniNest/felixmodule as is, and instead replacing neuron and synapse models with implementations provided by NEST during model initialization. We do not provide support for such changes. If you are, however, still interested in doing so, follow the instructions linked below.

If you want to install CogniNest from scratch, please follow the [installation instructions from source](https://nest-simulator.readthedocs.io/en/stable/installation/) on the NEST website. Please note that if you do not install NEST from source, it will not be possible to install the CogniNest module.

To install CogniNest from source, follow the instructions on the [GitHub repository](https://github.com/MaximeCarriere/cogninest).
