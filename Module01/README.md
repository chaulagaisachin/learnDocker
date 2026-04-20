# Module 01: Docker Install & Configure
 * [Monolithic and Microservice Architecture](https://github.com/chaulags/learnDocker/tree/main/Module01#monolithic-and-microservice-architecture)
 * [Virtualization v/s Containerization](https://github.com/chaulags/learnDocker/tree/main/Module01#virtualization-vs-containerization) 
 * [Introduction to Container & Docker](https://github.com/chaulags/learnDocker/tree/main/Module01#introduction-to-container--docker)
 * [Understanding the Docker components](https://github.com/chaulags/learnDocker/tree/main/Module01#understanding-the-docker-components--architecture)
 * [Docker Installation](https://github.com/chaulags/learnDocker/tree/main/Module01#docker-installation)
 * [What Has Changed (2026 Update)](https://github.com/chaulags/learnDocker/tree/main/Module01#what-has-changed-2026-update)
 * [Installation Path Recommended in 2026](https://github.com/chaulags/learnDocker/tree/main/Module01#installation-path-recommended-in-2026)
 * [Compose v2 Update](https://github.com/chaulags/learnDocker/tree/main/Module01#compose-v2-update)
 * [Post-Install Best Practices](https://github.com/chaulags/learnDocker/tree/main/Module01#post-install-best-practices)
 * [Quick Reality Check](https://github.com/chaulags/learnDocker/tree/main/Module01#quick-reality-check)


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
Wait for the Stuff to get loaded
\
![Loading](https://media3.giphy.com/media/1iNIkQBAwEkUuTpikf/giphy.gif?cid=ecf05e471xtdgy62d1gagagkvlqddvnyqpnadv7mirrymn55&rid=giphy.gif&ct=g)
\

## What Has Changed (2026 Update)

This module is still great for fundamentals. Here are the key updates for the current Docker ecosystem:
* Docker Engine is now commonly used together with BuildKit and integrated Compose v2.
* `containerd` is a core runtime component in the Docker stack and part of the wider OCI ecosystem.
* Many teams now use Docker for local/dev workflows, then deploy to Kubernetes or managed container platforms.

## Installation Path Recommended in 2026

The existing `docker.io` install still works for learning, but the official Docker repository is recommended for latest stable features.

Ubuntu/Debian (official repository):
```bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo \
	"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
	$(. /etc/os-release && echo \"$VERSION_CODENAME\") stable" | \
	sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Quick convenience method (official script):
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
```

Verify installation:
```bash
docker --version
docker compose version
```

## Compose v2 Update

* Modern Compose command is `docker compose` (with a space).
* Legacy `docker-compose` (with a hyphen) may still exist on older setups, but v2 plugin is the current default.

Example:
```bash
docker compose up -d
docker compose ps
```

## Post-Install Best Practices

* Run Docker without `sudo` by adding your user to the `docker` group:
```bash
sudo usermod -aG docker $USER
newgrp docker
```
* Quick smoke test:
```bash
docker run --rm hello-world
```
* For stronger workstation security, review Docker rootless mode:
	https://docs.docker.com/engine/security/rootless/

## Quick Reality Check

* Docker Swarm is still available and useful for learning orchestration basics.
* In most production environments today, Kubernetes is more common for large-scale orchestration.
* Learning Docker fundamentals first (this module) is still the right starting point.

**This is the End of Module 1**
[>> **Module 2**](https://github.com/chaulags/learnDocker/tree/main/Module02#module-02-image-creation--management--registry)
[ * * Go to Top * * ](https://github.com/chaulags/learnDocker/tree/main/Module01#module-01-docker-install--configure)
