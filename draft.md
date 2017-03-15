# Docker presentation

## Virtualization

### What is virtualization ?

### Why virtualization ?

 - Simplicity
 - Security
 - Risk reduction
 - Ease of management
 - HA ?

### What kind of virtualization ?

 - Full
 - Para virtualized
 - Containers

## Docker

 - Container
 - (LXC + cgroups)
 - High level API

### Why use docker

 - Versionning
 - Immutability
 - Repeatability
 - Same environment
 - Automation
(- Stateless)


### From a developper

 - We have the same environment for reporting bugs
 - If we do the same actions, we will get the same result
 - One command to try my application
 - Use it from your tests

### From an obs

 - I have the same environment everywhere
(- I can eplace containers)
 - I have versions attached to my deployed softwares
 - I can start application with one command line
 - Common environment to exchange with developpers
 - We can review deployements

### Vocabulary

 - Image
 - Container
 - Volume
 - Expose a port
 - link
 - SHA1 / names

### Sample commands

 - docker ps
 - docker ps -a
 - docker rm "container"

 - docker images
 - docker rmi image

 - docker run image
 - docker run --port "80:8080" image
 - docker run --link "dnsEntryUsed:linkedContainerName" image
 - docker run --volume "onMyComputer:inTheContainer" image
 - docker run --name "containerName" image

 - docker inspect container

 - docker exec container command

### DockerFile

#### What

Declarative file to build docker images

One line defines one intermediate image

#### How

Directives :

FROM 

   specifies origin image

   Exemple : FROM java:openjdk-8-jdk

ENV

   defines environment variables

   Exemple : ENV GIT_VERSION 1:2.1.4-2.1

WORKDIR

   Changes of directory

   Exemple : WORKDIR /root

RUN

   Run a command inside the container

   Exemple : RUN apt-get install -y git

COPY

   Copy files into the container

   Example : COPY compile.sh /root/compile.sh

ENTRYPOINT

   Defines the command to execute when starting the container

   Exemple : ENTRYPOINT ["/root/compile.sh"]

#### Exemple 

```
FROM java:openjdk-8-jdk

ENV GIT_VERSION 1:2.1.4-2.1

# Install Maven
WORKDIR /root
RUN wget http://mirrors.ircam.fr/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.tar.gz
RUN tar -xvf apache-maven-3.3.1-bin.tar.gz
RUN ln -s /root/apache-maven-3.3.1/bin/mvn /usr/bin/mvn

# Install git
RUN apt-get update
RUN apt-get install -y git

# Copy the script
COPY compile.sh /root/compile.sh
COPY integration_tests.sh /root/integration_tests.sh

# Define the entrypoint
WORKDIR /james-project
ENTRYPOINT ["/root/compile.sh"]
```

#### Docker build

 - docker build
 - docker build --tag "tag"
 - docker build path/to/dockerfile

#### Usefull links

https://docs.docker.com/engine/reference/builder/
https://docs.docker.com/engine/reference/run/
https://docs.docker.com/engine/reference/commandline/cli/

# Apache Web server

See Nicolas Doc

## Practice :-)

## Questions
