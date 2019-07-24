# [juditth.github.io](https://juditth.github.io/)

## Menu

\| [Home](https://juditth.github.io/)
\| [Install dockar to Raspberry Pi B from scratch](https://juditth.github.io/raspberryPiB)
\| [CZ Wordpress manual](https://juditth.github.io/wordpressManualCz)
\| [Docker play - part 1](https://juditth.github.io/dockerPlayPart1)
\| 

## Start

Get version of docker we have:

    docker version


TODO    
service docker stop 
service docker start 


## Processes

Get the processes running in docker

    docker ps
    #or
    docker container ls

## Docker images management

### Get image

To download image do docker there is command

    docker pull <image>
    #e.g. docker pull jenkins/jenkins

List of the available images is available in hub.docker.com.

If not version is specified docker will download the *latest* version. 

But also concrete version can be specified to be pulled:

    docker pull <image>:<version>
    #e.g. docker pull jenkins/jenkins:1.2.3

There exists special low-size version of most images for docker called *alpine*. They save space in docker and need less time to be downloaded of course. 

    #e.g. docker pull jenkins/jenkins:alpine

When image is pulled from the hub it looks like:

    pi@raspberrypi:~ $ docker pull jenkins/jenkins:alpine
    alpine: Pulling from jenkins/jenkins
    e7c96db7181b: Pull complete
    f910a506b6cb: Pull complete
    c2274a1a0e27: Downloading  39.24MB/70.73MB
    b85e4105cfd1: Download complete
    680560540b6c: Download complete
    ef06759e8b20: Download complete
    2ac4f38ea29e: Downloading  48.41MB/77.46MB
    721fb0e3e598: Download complete
    2eb8051404bc: Download complete
    24a10d6765ad: Download complete
    d0a9ec12100b: Download complete
    63b4622f9021: Download complete
    ff79ef061958: Download complete


### Delete image

There is of course a way how to delete the image from docker to e.g. save space: 

    docker rmi <image_id>
    e.g. docker rmi a34b13c421


### Explore image

    sudo docker inspect jenkins 
    inspect the docker image
    docker history ImageID 




## Docker container

### Run image as container
If the image is already installed in docker will be run immediatelly. If not will be download first and run after:

    docker run <image>
    # docker run jenkins

There is also a way how to run process and switch to it's terminal. *-i-* is interactive mode, *-t* is run as terminal. *<interpretor>* is used to run the e.g. bash shell once CentOS is up and running.

    sudo docker run –it <image> <interpetor>
    # e.g. sudo docker run –it centos /bin/bash


### Stop container

    docker stop ContainerID 

### Delete container

    docker rm ContainerID 
    sudo docker stats 9f215ed0b0d3 
    docker pause ContainerID
    docker unpause ContainerID
    docker kill ContainerID

### Docker container life cycle    

1. Initially, the Docker container will be in the *created* state.
2. Then the Docker container goes into the *running* state when the Docker run command is used.
3. The Docker kill command is used to *kill* an existing Docker container.
4. The Docker pause command is used to *pause* an existing Docker container.
5. The Docker stop command is used to *stop* an existing Docker container.
6. The Docker run command is used to put a container back from a stopped state to a running state.




