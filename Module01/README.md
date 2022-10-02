# Module 01: Docker Install & Configure
 * [Monolithic and Microservice Architecture](https://github.com/chaulags/learnDocker/tree/main/Module01#monolithic-and-microservice-architecture)
 * [Virtualization v/s Containerization](https://github.com/chaulags/learnDocker/tree/main/Module01#virtualization-vs-containerization) 
 * [Introduction to Container & Docker](https://github.com/chaulags/learnDocker/tree/main/Module01#introduction-to-container--docker)
 * [Understanding the Docker components](https://github.com/chaulags/learnDocker/tree/main/Module01#understanding-the-docker-components--architecture)
 * [Docker Installation](https://github.com/chaulags/learnDocker/tree/main/Module01#docker-installation)


## Monolithic and Microservice Architecture


| Monolithic Architecture      | Microservice Architecture |
| :-----------: | :-----------: |
| Traditional model of software/application program which is self contained.      | Modern way of software development that depends on modular component. |
| Independent from any other application.   | Dependent & modular but single module failure will not effect the whole application        |
| Singluar code base.   | Different plugins could have different code base        |
| Update requires to change in the entire stack.   | Easy update & speedy deployment        |
| Time Consuming.   | Much more fast & efficient.        |
| Difficult to pin point the problems for large problem.   | Easy debugging & code management.        |


![Differences between Monolithic and Microservice](https://miro.medium.com/max/1000/1*b5vneT_J4-dKejbYH4o5qg.png)
![Monolithic and Microservice](https://wac-cdn.atlassian.com/dam/jcr:b2be0d53-f4b2-46d8-9a34-993048cc6225/Monolith%20Vs%20Microservice%20image.png?cdnVersion=549)


## Virtualization v/s Containerization 

| Virtualization      | Containerization |
| ----------- | ----------- |
| Partitioning a physical server into multiple virtual servers.      | Bundling the application code along with the libraries, configuration files, and dependencies required for the application to run cross-platform.  |
| Hypervisor Layer performs all the task.   | Docker Daemon/Container Engine Layer performs all the task        |
| Hypervisor creates an abstract layer on the computer hardware, handles resource allocation, and monitors the virtual machines.   | Piece of software that accepts user requests, including command line options, pulls images, and from the end user's perspective runs the container.        |
| Deploys an actual virtual server on top of hardware resouces.   | Creates an environment for an application to run.      |
| Example: VMWare, VirtualBox   | Example: Docker, LXD, Podman        |
<!-- | Paragraph   | Text        | -->


![Virtualization versus Containerization](https://images.contentstack.io/v3/assets/blt300387d93dabf50e/bltb6200bc085503718/5e1f209a63d1b6503160c6d5/containers-vs-virtual-machines.jpg)

## Introduction to Container & Docker

Container:
* Standard unit of software that packages up code and all its dependencies.
* Runs quickly and reliably from one computing environment to another.
* Lightweight, standalone, executable package of software that includes everything needed to run an application
* Code, runtime, system tools, system libraries and settings.

Docker:
* Set of platform as a service products that use OS-level virtualization to deliver software in packages called containers.
* Program writtern in Go.
* Containerd is the underlying technology that is a part of docker engine.


## Understanding the Docker components & Architecture
Client Side
* Docker CLI: Command line interface that is allowed to issue command to docker daemon for various task.
* Remote API: RESTFul API accessed  by an HTTP client  such as 

Host Side
* Docker Daemon: Also known as dockerd, listens for Docker API requests/CLI commands and manages Docker objects such as images, containers, networks, and volumes.
* Images: Template file that is the foundation of a docker container.
* Containers: Running version of a docker images.

Docker Registry: Storage and distribution system for named Docker images. 
* Docker Hub
* AWS Container Registry
* Azure Container Registry

![Docker Architecture](https://docs.docker.com/engine/images/architecture.svg)

## Docker Installation

Ubuntu/Debian Linux
```
sudo apt update && sudo apt upgrade
sudo apt install docker.io docker-compose
```


[ * * Go to Top * * ](https://github.com/chaulags/learnDocker/tree/main/Module01#module-01-docker-install--configure)
