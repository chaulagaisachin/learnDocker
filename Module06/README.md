# Module 06: Container Orchestration using Docker Swarm
* Container Orchestration
* Docker Swarm Introduction
* Docker Swarm Architecture
* Docker Swarm and distribution strategies
<!-- * Installing Docker EE
* Installing Universal Control Plane ( UCP )
* Creating Users and Teams in UCP
* DTR Installation
* Create a DTR Repo
* Users and Teams in DTR
* Setting Permissions in DTR -->

## Container Orchestration
* Container Orchestration is the process of delpying and maintainig large number of containers & services for the application to run as intended.
* Automation of much of the operational effort required to run containerized workloads and services.
* Manage a container's lifecycle, including provisioning, deployment, scaling (up and down), networking, load balancing and more.
* Why Orchestration?
  * **Configuration and scheduling**
  * **Provisioning and deployment**
  * **Health monitoring**
  * **Resource allocation**
  * **Redundancy and availability**
  * **Updates and upgrades**
  * **Scaling or removing containers to balance workloads across the infrastructure**
  * **Moving containers between hosts**
  * **Load balancing and traffic routing**
  * **Securing container interactions**
![benefit](https://www.splunk.com/content/dam/splunk2/images/data-insider/container-orchestration/benefits-of-container-orchestration.jpg)

* Container Orchestration Tools
  * Docker Swarm
  * Kubernetes
  * Mesos Marathon
  * Nomad
  * Amazon EKS
  * Azure Kubernetes Service

![lifecycle](https://k21academy.com/wp-content/uploads/2020/11/458769_1_En_14_Fig2_HTML-1.png)


## Docker Swarm Introduction
* Docker Swarm is another popular open source container orchestration platform.
* The cluster management and orchestration features embedded in the Docker Engine are built using swarmkit.
* [Swarmkit](https://github.com/moby/swarmkit) is a separate project which implements Docker’s orchestration layer and is used directly within Docker.

## Docker Swarm Architecture

![architecture](img/DockerArchitecture.png)

* **Services & Tasks**: 
  * A service is the definition of the tasks to execute on the manager or worker nodes.
  * It is the central structure of the swarm system and the primary root of user interaction with the swarm.
  * For global services, the swarm runs one task for the service on every available node in the cluster.
  * **Task** carries a Docker container and the commands to run inside the container. It is the atomic scheduling unit of swarm.
  * Manager nodes assign tasks to worker nodes according to the number of replicas set in the service scale.


* **Nodes**: A node is an instance of the Docker engine participating in the swarm.
  * **Manager Nodes**:
    * To maintain the cluster state.
    * Allocating IP addresses to tasks.
    * Accepting commands and creating service objects.
    * Assigning tasks to nodes.
    * Scheduling of Services.
    * Serving Swarm mode HTTP API endpoints.
    * Instructing a worker to run a task.
  * **Worker Nodes**
    * A manager node can exist without a worker node
    * Worker cannot exist without a manager.
    * Manager node assigns tasks to worker nodes.


## Docker Swarm Tutorials
This part mostly contains the working of docker swarm and its deployment features.

#### Initialize Docker Swarm
* Following command will help you understand sub-feature of the docker-swarm.
```
sudo docker swarm help
```
![swarm-help](img/swarm-help.png)

* To initialize the docker swarm:
```
sudo docker swarm init --advertise-addr <ipAddress>
```
![swarm-init](img/swarm-init.png)

* The above system is now the manager for the docker swarm cluster.
* We can also see the instruction to add more worker to this swarm cluster.
![swarm-join](img/swarm-join.png)

* To view more info about the docker-swarm:
```
sudo docker info ;; Go to Swarm Section
```
![swarm-info](img/swarm-info.png)

#### Manage Nodes in Docker Swarm Cluster
* To view all the nodes:
```
sudo docker node ls
```
![swarm-node](img/swarm-node.png)

* How worker node leave a docker swarm.
```
sudo docker swarm leave --force
```
![swarm-left](img/swarm-left.png)

![swarm-down](img/swarm-down.png)

* Remove the node from docker swarm cluster's manager.
```
sudo docker node rm <nodeID>
```
![swarm-removed](img/swarm-removed.png)

* If you want to view the token to add a worker to the cluster,
```
sudo docker swarm join-token worker
```
* If you want to add a node as **MANAGER**n the token to add a node to the cluster,
```
sudo docker swarm join-token manager
```
![swarm-join-advance](img/swarm-join-advance.png)

<!-- ## Installing Docker EE
## Installing Universal Control Plane ( UCP )
## Creating Users and Teams in UCP
## DTR Installation
## Create a DTR Repo
## Users and Teams in DTR
## Setting Permissions in DTR -->