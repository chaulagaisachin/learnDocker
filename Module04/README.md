# Module 04: Docker Networking
* [Accessing containers](https://github.com/chaulags/learnDocker/tree/main/Module04#accessing-containers)
* [Exposing container ports](https://github.com/chaulags/learnDocker/tree/main/Module04#exposing-container-ports)
* [Docker Networking](https://github.com/chaulags/learnDocker/tree/main/Module04#docker-networking)
* [Docker Networking Drivers & Connectivity](https://github.com/chaulags/learnDocker/tree/main/Module04#docker-networking-drivers--connectivity)
* [Naming Our Containers](https://github.com/chaulags/learnDocker/tree/main/Module04#naming-our-containers)
* [Configure Docker to use external DNS](https://github.com/chaulags/learnDocker/tree/main/Module04#configure-docker-to-use-external-dns)
* [Docker Events](https://github.com/chaulags/learnDocker/tree/main/Module04#docker-events)
* [Official Documentation Updates (2026)](https://github.com/chaulags/learnDocker/tree/main/Module04#official-documentation-updates-2026)
* [Port Publishing Update](https://github.com/chaulags/learnDocker/tree/main/Module04#port-publishing-update)
* [Network Drivers and Service Discovery Update](https://github.com/chaulags/learnDocker/tree/main/Module04#network-drivers-and-service-discovery-update)
* [Docker Compose Networking Update](https://github.com/chaulags/learnDocker/tree/main/Module04#docker-compose-networking-update)
* [DNS Configuration Update](https://github.com/chaulags/learnDocker/tree/main/Module04#dns-configuration-update)
* [Docker Events Update](https://github.com/chaulags/learnDocker/tree/main/Module04#docker-events-update)

## Accessing containers
* There are different ways to connect to a docker container.
* Some of them are listed below.
```
sudo docker attach <containerID>
```
```
sudo docker exec -it <containerID> <command | Bash>
```
* However, these techinques are not limited to above mentioned techniques. 
* We can also connect to varios container through network. 
* So, first of all, we'll discuss about docker containers.

## Exposing container ports
* There are many cases where you need to expose various services.
* Port are required to make the service available for the consumers.
* Exposing ports will help to forward port from docker container to host network adapter.
* Following commands can be used to forward | Expose port
  - -p [HOST_IP:]HOST_PORT:CONTAINER_PORT :: forward a specific container port to host
  - -P :: publish all exposed container ports to random host ports
```
sudo docker run -it -p 80:80 <imageName>
```
```
sudo docker run -dit --name my-running-app -p 8080:80 httpd:latest
```
![httpd-forward-port](img/httpd-forward-port.png)

![httpd-works](img/httpd-working-test.png)

* Now the service is available to the host's port.
* Same technique can be used to forward other service such as SSH.

## Docker Networking
* One of the reasons Docker containers and services are so powerful is that you can connect them together, or connect them to non-Docker workloads.
* Docker networking allows you to attach a container to as many networks as you like. 
* Docker networking enables a user to link a Docker container to as many networks as he/she requires. 
* Docker Networks are used to provide complete isolation for Docker containers.


## Docker Networking Drivers & Connectivity
* Docker supports networking for containers through network drivers.
* Different drivers are used for different connectivity and isolation requirements.

| Network Type | Description |
|---|---|
| Bridge | The default network driver.  If you don’t specify a driver, this is the type of network you are creating. Bridge networks are usually used when your applications run in standalone containers that need to communicate |
| Host | For standalone containers, remove network isolation between the container and the Docker host, and use the host’s networking directly. |
| None | For this container, disable all networking. Usually used in conjunction with a custom network driver. None is not available for swarm services. |
| Overlay | Overlay networks connect multiple Docker daemons together and enable swarm services to communicate with each other. You can also use overlay networks to facilitate communication between a swarm service and a standalone container, or between two standalone containers on different Docker daemons. This strategy removes the need to do OS-level routing between these containers. |
| MacVlan | Macvlan networks allow you to assign a MAC address to a container, making it appear as a physical device on your network. |
| IPVlan | IPvlan networks give users total control over both IPv4 and IPv6 addressing. |

* Legacy note: container linking with `--link` is a legacy feature. Modern container-to-container communication should use user-defined networks and built-in DNS-based discovery.


#### Docker Network Connection
```
sudo docker network --help
```
![docker-network](img/docker-network-help.png)

```
sudo docker network ls
```
![docker-network-ls](img/docker-network-ls.png)

```
sudo docker network inspect <networkID>
```
![docker-inspect](img/docker-network-inspect.png)

#### Create Different Network
```
sudo docker network create --help
```
![docker-create-network-help](img/docker-network-create-help.png)

```
sudo docker network create -d <driver> <networkName>
```
![create-network](img/create-network.png)

#### Change Network Adapters
```
sudo docker run -it --network <networkID> <imageName:version>
```
* To see what network(s) your container is on:
```
sudo docker inspect <container ID> -f "{{json .NetworkSettings.Networks }}"
```
* To disconnect your container from the first network
```
docker network disconnect <network Name> <container ID>
```
* Then to reconnect it to another network
```
sudo docker network connect <network Name> <container ID>
```
* To check if two containers (or more) are on a network together
```
sudo docker network inspect <network Name> -f "{{json .Containers }}"
```

## Naming Our Containers
```
sudo docker run -it --hostname web ubuntu:latest
```
* `--hostname` sets a hostname inside that container, but service discovery between containers is usually done through Docker networks and container/service names.

## Configure Docker to use external DNS
* There are two ways to change the docker DNS setting.
* By adding another parameter while running a container
  - --dns DNSServerAddress
```
sudo docker run --dns <DNSServerAddress>
```
* Changing the default DNS address for the whole docker architecture.
```
sudo vim /etc/docker/daemon.json
```
* Add following:
```
{
  "dns": ["8.8.8.8"]
}
```
* Also, restart your docker daemon.
```
sudo systemctl restart docker
```
## Docker Events
* Use *docker events* to get real-time events from the server. 
* These events differ per Docker object type.
* Different event types have different scopes. 
  * Container
  * Volume
  * Network
  * Images
  * Daemon
  * Service
  * Secrets
  * Config
* Go to this [link](https://docs.docker.com/reference/cli/docker/system/events/) for further information.
* You can use docker event by
```
sudo docker events
```
* Now all the activity will be reported through the output.
* You can also use linux redirections to store all these outputs.

![docker-events](img/docker-events.png)

## Official Documentation Updates (2026)

This module is still useful for fundamentals. The sections below add current Docker networking guidance based on official documentation.

## Port Publishing Update

* Use `-p` to publish specific ports and `-P` to publish all exposed ports.
* You can bind to a specific host interface, for example `127.0.0.1:8080:80`, to avoid publishing on all interfaces.
* If no host IP is provided with `-p`, Docker publishes on all interfaces by default.

Examples:
```bash
docker run -d -p 8080:80 nginx:latest
docker run -d -p 127.0.0.1:8080:80 nginx:latest
docker run -d -P nginx:latest
```

Official references:
* https://docs.docker.com/network/
* https://docs.docker.com/reference/cli/docker/container/run/

## Network Drivers and Service Discovery Update

* Default `bridge` and user-defined `bridge` networks behave differently for name resolution.
* User-defined bridge networks provide built-in DNS-based container name resolution.
* Use user-defined networks when multiple standalone containers need reliable service discovery.

Example:
```bash
docker network create my-net
docker run -d --name web --network my-net nginx:latest
docker run -it --rm --network my-net busybox:latest ping web
```

Official references:
* https://docs.docker.com/network/drivers/
* https://docs.docker.com/network/drivers/bridge/
* https://docs.docker.com/network/#dns-services

## Docker Compose Networking Update

* Docker Compose creates a project network by default.
* Services on the same Compose network can reach each other by service name.

Official reference:
* https://docs.docker.com/compose/networking/

## DNS Configuration Update

* `--dns` applies DNS configuration to a specific container.
* Daemon-level DNS in `/etc/docker/daemon.json` applies as default for newly started containers.
* Restarting the Docker daemon is required after daemon-level DNS changes.

Official references:
* https://docs.docker.com/network/#dns-services
* https://docs.docker.com/engine/daemon/

## Docker Events Update

* `docker events` supports filters and time windows to focus troubleshooting output.

Examples:
```bash
docker events --filter type=container
docker events --since 10m --until 1m
```

Official reference:
* https://docs.docker.com/reference/cli/docker/system/events/


[>> **Module 5**](https://github.com/chaulags/learnDocker/tree/main/Module05#module-05-docker-image-creation--compose)

[* * * Go To Top * * * ](https://github.com/chaulags/learnDocker/tree/main/Module04#module-04-docker-networking)
