# Module 05: Docker Image Creation & Compose
* [Introduction to DockerFile]()
* [Describe Dockerfile options]()
* [Write Dockerfile to create Image]()
* [Multi-Stage Dockerfile]()
* [Docker Compose]()
* [Understanding docker-compose file]()

## Introduction to DockerFile
* A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.
* Dockerfile is used for automation of work by specifying all step that we want on docker image.
* Dockerfile is automated script of Docker images
* Manual image creation will become complicated when you want to test same setup on different OS flavor
* You have to create image for all flavor but by small changing in dockerfile you can create images for different flavor
* It have simple syntax for image and do many change automatically that will take more time while doing manually.
* Dockerfile have systematic step that can be understand by others easily and easy to know what exact configuration changed in base image.
![dockerfile-intro](img/dockerfile-intro.png)


## Describe Dockerfile options
![dockerfile-options](https://raw.githubusercontent.com/sangam14/dockercheatsheets/master/dockercheatsheet7.png)

#### Example of a DockerFile
```
FROM ubuntu:latest 
RUN apt update 
RUN apt install –y apache2 
RUN apt install –y apache2-utils 
RUN apt clean 
EXPOSE 80
CMD [“apache2ctl”, “-D”, “FOREGROUND”]
```
* Copy all the codes above and paste it into a file name DockerFile.
* To build the docker image, go to the working directory and run following commands.

```
sudo docker build -t apacheimage:1.0 .
```
* View the created image using.
```
sudo docker images
```
* To run the built docker image.
```
sudo docker run --name myapache -d -p 80:80 apacheimage:1.0
```

## Write Dockerfile to create Image


## Multi-Stage Dockerfile


## Introduction to Docker Compose


## Understanding docker - compose file

