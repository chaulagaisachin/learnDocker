# Module 03: Docker Storage and Volumes
 * [Understanding the need for Volume service in Docker]()
 * [Create and Manage volumes]()
 * [Start a container with a volume]()
 * [Populate data in a volume using a container]()
 * [Use a read - only volume]()
 * [Understanding various Volume Plug-ins]()

 ## Understanding the need for Volume service in Docker
 * Docker volumes are file systems mounted on running containers to make sure that the data generated is preserved even after the docker container is stopped.
 * Its independent to the container and its lifecycle.
 * This helps to share required files from host system to container.
 * This storage implementation is also optimal for retrieving, storing, and transferring images across various environments.
 * Can be mapped to local storage.
 * Multiple container can use the same volume.

 ## Create and Manage volumes
 * We can use following commands to view volume information.
 * Try to explore more about volume using the following information:
```
sudo docker volume --help
```
![docker-volume-help](img/docker-volume-help.png)

```
sudo docker volume ls
```
![docker-volume-ls](img/docker-volume-ls.png)


 #### Create a volume & Inspect.
 * To create a volume, use following commands:
```
sudo docker volume create <volumeName>
```
 * To view information about the volume just created, use:
```
sudo docker volume inspect <volumeName> 
```
![docker-volume-created-inspect](img/docker-volume-created-inspect.png)

 
 ## Start a container with a volume
 * *--mount* will help to mount the volume create. Make sure to give proper parameters.
 ```
 sudo docker run -it --name <contName> --mount source=<volName>,target=<insideTheContainer> <dockerImage:versionNumber>
 ```
 
 * We can also use different way to mount a docker volume.
```
sudo docker run -it -v <source>:<target> <dockerImage:versionNumber>
```
![docker-mount](img/docker-volume-mounted.png)

 ## Populate data in a volume using a container

![docker-write-file](img/write-newVolume.png)

![view-created-file](img/view-new-file.png)

 ## Use a read - only volume


 ## Understanding various Volume Plug-ins

