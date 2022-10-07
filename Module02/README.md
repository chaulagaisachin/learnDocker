# Module 02: Image Creation , Management & Registry
* [Docker Registry](https://github.com/chaulags/learnDocker/tree/main/Module02#docker-registry)
* [Docker Images & Layers](https://github.com/chaulags/learnDocker/tree/main/Module02#docker-images--layers)
* [Working with Containers](https://github.com/chaulags/learnDocker/tree/main/Module02#working-with-containers)
* [Process in Containers](https://github.com/chaulags/learnDocker/tree/main/Module02#process-in-containers)
* [Docker Lifecycles](https://github.com/chaulags/learnDocker/tree/main/Module02#docker-lifecycles)
* [Docker Commit & Push](https://github.com/chaulags/learnDocker/tree/main/Module02#docker-commit--push)

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
\
![Downloading](https://media3.giphy.com/media/3o7WTAkv7Ze17SWMOQ/giphy.gif?cid=790b76115d7878163a2bd03d2a72099f218f17e42b0be33e&rid=giphy.gif&ct=g)

* Wait for some seconds or minutes, depending upon your internet speed.
* After the pull operation has been completed, use following commands to view your docker images in you local system.
```
docker images
```
\
![Docker Images](img/docker-images.jpg)

* To run the image, use:
```
docker run -i -t ubuntu:latest
```
* The above command's options are described as follows:
  * -i = Interactive session with the container that is to be executed.
  * -t = To make sure the session is interactive, a terminal is required. Hence.
  * ubuntu/ubuntu:latest = imageName:versionNumber 
\
![docker-run](img/docker-run.jpg)

* You can now interact with the running docker container through a terminal.


## Working with Containers
* As you already have opened an interactive session by running a docker cotainer.
* Wait a second before doing anything. Let's understand whats happenning here.
* Docker Image is just a template for a container. Container is a executed version of the template environment itself.
\
![Docker Image & Container](https://davetang.github.io/reproducible_bioinformatics/assets/docker_image.png)
* We can also view the information about container and its state, the following commands can be used.
```
docker ps -a
```
  * -a = Show all containers (default shows just running)
\
![docker-ps-a](img/docker-ps-a.jpg)

* Remember the information you have here. You'll be coming here a lot.
* You have a terminal access to the docker container running a Ubuntu.
* Use it for a while, see if you can find any difference.
\
![Waiting Meme](https://c.tenor.com/ycKJas-YT0UAAAAM/im-waiting-aki-and-paw-paw.gif)

* There are various information about the container:
  * Container ID = Randomly generated ID to specify the running instance. 
  * Image = The image used to create the container.
  * Command = The command executed while running the container(for ubuntu "bash" was executed)
  * Created = Time it has been created for.
  * Status = Running, exited.
  * Port = Any exposed ports
  * Name = Randomly generated name, these names can be used instead of container ID to execute stuffs. --name option can be used to give your own name for the container. 
* Now Exit the terminal.
* Use the command that displays the [information about container and its state](https://github.com/chaulags/learnDocker/tree/main/Module02#working-with-containers).
* What are the changes you can see ?
\
![Use your power meme](https://c.tenor.com/lTPOYFTdUKgAAAAC/you-ready-to-use-your-power-for-good-chris-cantada.gif)

* Note down the changes you see.
\
![docker-stop](img/docker-ps-aa.jpg)



## Process in Containers
* You can also run a container without attaching to it.
```
docker run -it -d ubuntu:latest
```
\
![docker-detach](img/docker-detach.png)
* View the information about running container
* You can also attach yourself to docker container using:
```
docker attach <container id>
```
\
![docker-attach](img/docker-attach.png)

* You can also using following key combination for detaching yourself from a container:
```
CTRL + Q
```
* You can also view running processess without attaching to a docker container.
```
docker top <container id>
```
\
![docker-top](img/docker-top.png)

NOTE: do start ssh service in you container the view similar output as above.

## Docker Lifecycles
* Docker container can also be just created without running it.
```
sudo docker container create -i -t --name myUbuntu ubuntu:latest
```
\
![docker-create](img/docker-create.png)

* Now the created docker container can be started using following commands:
```
sudo docker container start --attach -i myUbuntu
```
\
![docker-create-start](img/docker-create-start.png)
\
* Docker status can be checked
![docker-started](img/docker-created-started.png)

* From this illustration , we can undertand that above commands are equivalent to:
```
sudo docker run -it --name myUbuntu ubuntu:latest
```

* Following are the Docker lifecycle stages:
![docker-lifecycle](https://k21academy.com/wp-content/uploads/2020/10/Capture-5.png)


## Docker Commit & Push
* Docker container are just an instance of a docker images.
* If the docker container are deleted, any changes will be deleted with it.
* So, if a docker image is to be saved, we can use docker commit.
* Docker commit will create your container's instance as an image.
* So next time you start this new image, it will have everything you want.

```
docker commit <containerID> <newImageName:version>
```
![docker-commit](img/docker-commit.png)

* Congratulation on you first commit.
* This images of yours can also be uploaded to DockerHub, but first you need to login.
```
docker login
```
![docker-login](img/docker-login.png)

* Now use following commands to push your image to DockerHub

```
docker push <imageName:version>
```
But,
![but-meme](https://c.tenor.com/mv8WDfaaqZkAAAAC/but-nevermind-meme.gif)

* You get error, something like this:
![docker-push-error](img/docker-push-denied.png)

* That's because you also need to specify your repository.
* You can do that using docker tag.

```
docker tag <imageID> <repoName/imageName:versionNumber>
```
![docker-tag](img/docker-tag.png)

* Now, Let's Push again
![docker-push-success](img/docker-push-success.png)

**This is the end of module 2**

[>> **Module 3**](https://github.com/chaulags/learnDocker/tree/main/Module03#module-03-docker-storage-and-volumes)

[* * * Go To Top * * * ](https://github.com/chaulags/learnDocker/tree/main/Module02#module-02-image-creation--management--registry)


