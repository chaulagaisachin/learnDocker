# Module 02: Image Creation , Management & Registry
* [Docker Registry](https://github.com/chaulags/learnDocker/tree/main/Module02#docker-registry)
* [Docker Images & Layers](https://github.com/chaulags/learnDocker/tree/main/Module02#docker-images--layers)
* [Working with Containers](https://github.com/chaulags/learnDocker/tree/main/Module02#working-with-containers)
* [Process in Containers](https://github.com/chaulags/learnDocker/tree/main/Module02#process-in-containers)
* [Docker Lifecycles](https://github.com/chaulags/learnDocker/tree/main/Module02#docker-lifecycles)
* [Docker Commit](https://github.com/chaulags/learnDocker/tree/main/Module02#docker-commit)

## Docker Registry
* Let's start with simple docker image.
* Docker Hub will be used to fetch the docker images from the docker registry.
* Go and create and account in [Docker Hub](https://hub.docker.com/) so that it will help you help you in later chapters.

```
    https://hub.docker.com/
```
![DockerHub Image](https://static.packt-cdn.com/products/9781789137231/graphics/assets/01327d92-d3d2-4354-98bb-2a443adad38d.png)

## Docker Images & Layers
* Search "Ubuntu" in docker hub to find the base template of the ubuntu OS.
* You can see offical docker image for Ubuntu.
![DockerHub](img/ubuntu-search.jpg)
* Use following commands to download/pull the image to your own local system.
```
    docker pull ubuntu
```
* If you are having any error such as permission denied or similar, use:
```
    sudo docker pull ubuntu
```
* Wait for some seconds or minutes, depending upon your internet speed.
* After the pull operation has been completed, use following commands to view your docker images in you local system.
```
    docker images
```
* To run the image, use:
```
    docker run -i -t ubuntu/ubuntu:latest
```
* The above command's options are described as follows:
  * -i = Interactive session with the container that is to be executed.
  * -t = To make sure the session is interactive, a terminal is required. Hence.
  * ubuntu/ubuntu:latest = publisherName/imageName:versionNumber 

## Working with Containers
* As you already have opened an interactive session by running a docker cotainer.
* Wait a second before doing anything. Let's understand whats happenning here.
* Docker Image is just a template for a container. Container is a executed version of the template environment itself.
![Docker Image & Container](https://davetang.github.io/reproducible_bioinformatics/assets/docker_image.png)
* We can also view the information about container and its state, the following commands can be used.
```
    docker ps -a
```
  * -a = Show all containers (default shows just running)
* You have a terminal access to the docker container running a Ubuntu.
* Use it for a while, see if you can find any difference.
![Waiting Meme](https://i.pinimg.com/originals/42/3c/37/423c375c2e12c1a708ecc1694e472ff1.gif)

## Process in Containers

## Docker Lifecycles


## Docker Commit

