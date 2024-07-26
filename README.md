## DOCKER
* Docker is a set of platform as a service products that use OS-level virtualization to deliver software in packages called containers

### PROBLEMS FACED
- Compatibility with os, between apps (matrix from hell)
- Long setup time

### SOLUTION
- **Containers**
    - Isolated runtime environment (LXC, LXD, LXCFS), Docker uses LXC
    - Helpt to package, containerize and ship applications
    - They are lightweight and less isolated than dedicated VM
    - Docker uses kernel of ubderlying host (kernel sharing)
- Hypervisor: Daemon which runs and maintain multiple VMs

## DOCKER COMMANDS

| COMMAND                                                                   | EFFECT                                                                        |
|---------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| `docker run IMAGE_NAME`                                                   | Run a container                                                               |
| `docker run -d IMAGE_NAME`                                                | Run in detached mode                                                          |
| `docker run IMAGE_NAME [COMMANDS]`                                        | Run a command in a container                                                  |
| `docker run -it IMAGE_NAME [COMMAND]`                                     | Interactive pseudo terminal mode                                              |
| `docker run -e VAR=VAL IMAGE`                                             | Pass env var to container                                                     |
| `docker run --name=CONTAINER_NAME IMAGE_NAME`                             | Give name to container                                                        |
| `docker run -v VOLUME_NAME:/path/in/container`                            | Mount a volume                                                                |
| `docker run IMAGE --network=NETWORK_NAME`                                 | Specify network to attach for container                                       |
| `docker kill CONTAINER_ID`                                                | Kill a container                                                              |
| `docker attach CONTAINER_ID`                                              | Attach to a detached container                                                |
| `docker ps`                                                               | List all running containers                                                   |
| `docker ps -a`                                                            | List all containers (even the stopped or exited ones)                         |
| `docker container stats` | See resource usage for all docker containers |
| `docker container top CONTAINER_ID` | See the processes and their behaviour in container |
| `docker container logs CONTAINER_ID` | Get container logs |
| `docker container logs -f CONTAINER_ID` | Watch realtime logs for containers |
| `docker container pause CONTAINER_ID` | Pauses the container in the same state |
| `docker container unpause CONTAINER_ID` | Resume a paused container |
| `docker container stop CONTAINER_ID` | Tries to kill the container gracefully, but if container does not quit, it force kills it (like but not SIGTERM, SIGKILL) |
| `docker container kill --signal=SIGNAL CONTAINER_ID` | We can kill docker containers like the normal kill command |
| `docker stop CONTAINER_ID`                                                | Stop a container                                                              |
| `docker rm CONTAINER_ID`                                                  | Remove container                                                              |
| `docker images`                                                           | List all images                                                               |
| `docker inspect CONTAINER_ID`                                             | Inspect container properties                                                  |
| `docker logs CONTAINER_ID`                                                | Get logs for container                                                        |
| `docker rmi IMAGE_ID`                                                     | Remove image                                                                  |
| `docker pull IMAGE_ID`                                                    | Pull image from remote repository                                             |
| `docker exec CONTAINER_ID COMMANDS`                                       | Execute a command in a running container                                      |
| `docker build -t ACCOUNT_CONTEXT_NAME/IMAGE_NAME:TAG .`                                        | Build a image with image and tag                                              |
| `docker build . -f DOCKERFILE_NAME -t ACCOUNT_CONTEXT_NAME/IMAGE_NAME:TAG` | Build a image whose `Dockerfile` filename is different |
| `docker volume create VOLUME_NAME`                                        | Create a volume                                                               |
| `docker network create --driver=DRIVER --subnet SUBNET_CIDR NETWORK_NAME` | Create a network                                                              |
| `docker network ls`                                                       | Show available networks                                                       |
| `docker system df`                                                        | Show disk usage for docker images, containers and volumes                     |
| `docker system events --since TIME` | See docker events and TIME format is `NUMBER_HOURS`, `NUMBER_MINUTES` |

> [!TIP]
> When we are connected to a container in interactive mode and we want to exit but keep the container running in background, we can do so by `<ctrl>+<p>+<q>`

> [!TIP]
> * Kill all containers: `docker kill $(docker ps -aq)`
> * Remove all containers: `docker container rm $(docker container ls -aq)`
> * Remove all volumes: `docker volume rm $(docker volume ls -q)`

> [!tip]
> * Remove all docker stopped containers and reclaim space: `docker container prune`
> * We can also make a container autoremove itself after exiting with the `--rm` flag in docker run command

> [!TIP]
> * Remove all unused docker images
> * Then to clear up the space run `docker image prune -a`

> [!TIP]
> * Rename containers: `docker container rename OLD_NAME NEW_NAME`
> * Give hostname to a container using `--hostname=HOST_NAME` while running the container
> * Two or more containers can have same hostname but container name should be unique

> [!IMPORTANT]
> * Restart policy can be assigned while running containers using `--restart=RESTART_POLICY`
> * Restart policies are: `no`, `on-failure`, `always`, `unless-stopped`

> [!IMPORTANT]
> * While mapping ports, be careful about the format `HOST_PORT_PUBLISH_PORT:CONTAINER_PORT`
> * The `HOST_PORT` is used when using docker in single machine, `PUBLISH_PORT` is user while using in swarm and they must be free when we assign them
> * A computer have multiple IP addresses as it is connected to multiple networks and if we choose to expose the docker service to only one IP and network, we can do it by `IP:HOST_PORT_PUBLISH_PORT:CONTAINER_PORT`
> * If we do not define a expose or publish port, a random port is assigned from the range [32768, 60999] and this setting is stored at `/proc/sys/net/ipv4/ip_local_port_range`
> * If we pass no ports and pass `-P` parameter, the `EXPOSE_PORT` defined in the image file through `Dockerfile` is exposed to host
> * To see exposed ports by a container see expose ports section in docker inspect output
> * All this process of port publishing depends on `iptables`

## DOCKER IMAGES
* Creation: `Dockerfile`
* `Instruction` `Argument` format of file
* All capitalized words are instructions, rest on the right are arguments

### DOCKERFILES
* Layered architecture: each instruction line creates a new layer with just the changes from the previous layer
* When we modify the `dockerfile` and rebuild the image, only those instructions (including the added and deleted and modified and the instructions after them) are rebuilt, not from the first one
> [!IMPORTANT]
> #### CMD VS ENTRYPOINT
> * The parameters we specify in run command get totally replaced in `CMD`
> * The parematers we specify in run command get appended to the `ENTRYPOINT` specified in the `Dockerfile`
> * Also we get a flexibility to execute containers with dynamic variables through the run command while using `ENTRYPOINT`
> ```
> EXAMPLE
> # CMD
> RUN ["sleep", "10"]
> docker run image sleep 10
> 
> # ENTRYPOINT
> ENTRYPOINT ["sleep"]
> docker run image 10
> ```
> * Setting default parameters for all the variables is important to avoid mishaps we can do the following by:
> ```
> ENTRYPOINT ["sleep"]
> CMD ["5"]
> ```

> [!IMPORTANT]
> ### IMAGE NAMING CONVENTION
> * The properformat to refer am image is `REGISTRY_IP_HOSTNAME/ACCOUNT_CONTEXT_NAME/IMAGE_NAME:TAG`
>     - `REGISTRY_IP_HOSTNAME` refers to the registry from where we fetch the image, default value is `docker.io`
>     - `ACCOUNT_CONTEXT_NAME` refers to the account on registry that published the image, sometimes we write only the `IMAGE_NAME` - that case is where the `ACCOUNT_CONTEXT_NAME` is same as `IMAGE_NAME`
>     - `IMAGE_NAME` name of the image
>     - `TAG` is the version or a specific notation, when we do not specify it it gets default value as `latest`

> [!IMPORTANT]
> ### BUILD CONTEXTS
> * Remember the `.` that we pass while building the image, this is called the build context
> * It is the path of directory where the dockerfile is present
> * When we copy data inside image using dockerfile using `COPY` command we pass an directory or file
> * What if there are files in the directory that are not needed in building ? We can ignore them using `.dockerignore` file
> * This can reduce image size and build time
>
> ### REMOTE BUILD CONTEXTS
> * Instead of having a local context, we can have remote context and simplify the process
> * We can do this for private repositories too, [see here](https://docs.docker.com/build/building/context/#git-repositories) 
>
> | COMMAND | EFFECT |
> | ------- | ------ |
> | `docker build GITHUB_REPO` | Remote build context |
> | `docker build GITHUB_REPO#BRANCH_NAME_COMMIT_ID` | Remote build context with branch or commit ID |
> | `docker -f DOCKERFILE_NAME GITHUB_REPO#BRANCH_NAME:DIRECTORY` | Remote build context with dockerfile path and (branch or commit ID) and directory |

### BUILD CACHE
* Each layer is cached while build process and the layers which are not required to rebuild are build from cache while re building process

> [!TIP]
> **SENARIO**
> * Suppose in a Dockerfile we are updating apt and then installing the packages and we are rebuilding after months and we have added an package and need pudated packages in rest of the packages, sisce updating apt repositories is cached and wont be rebuilt and repositories wont be updated
> * To solve this issue, repository updation and package installation should be part of a single instruction which will force the repositories to be updated 
> Example : `RUN apt-get update && apt-get install -y PACKAGE1 PACKAGE2`
> But this creates a build issue too because if we add or delete or modify a package, all packages will be installed again from scratch instead of caching and build will be taking time
> All instructions that change less frequently should be at top of the Dockerfile and the ones which are changed frequesntly at the bottom of the Dockerfile
>
    > [!IMPORTANT]
    > * HUHU

## DOCKER STORAGE
* `/var/lib/docker/` is the path for docker data like image data, container data etc
* Docker images are in layered arcitecture
* Copy-On-Write mechanish for layers
* Docker volume creates virtual buld sturage devices that can be used in the container
* **Docker Storage Drivers**
    - AUFS
    - ZFS
    - BTRFS
    - Device Mapper
    - Overlay
    - Overlay2

* Docker volume filename for a container in `/var/lib/docker/volumes` is same as the container ID
* While recreating a layer if one layer hanges, then the layers after that layer are recreated too as we can't be sure that the rest layers will be the same


## DOCKER NETWORKING
* Default networks are: `bridge`, `none` and `host`
* Default CIDR for docker networks is `172.17.0.0/16`
* If we specify `--network=host` we can directly access the app on the host system on the same port that app is running in the container
* If we specify `--network=none` thwy do not get attached to any network
* All docker containers can resolve each other using the container names, docker has builtin DNS.

## DOCKER SWARM
* **CONTAINER ORCHESTRATION**: A process of maintaining accessability security and reliability of multiple containers
* Combines multiple docker engines together into a single cluster for high availability and load balancing
* There are swarm managers and worker nodes
* Master node mantains the cluster state and manages the entire cluster, adding managing nodes, distributing services and responsibility
* There should be multiple manager nodes in odd numbers

* **Node Types:**
- Worker 
- Master
    - Leader
    - Follower

### HOW MANAGERS WORK ?
* Only one manager is allowed to make decisions
* Only one node can't make all decisions on its own, it has to be mutually agreed upon by other manageers too
* Docker solves this by `RAFT Consensus`
- Random timer kicks off and the worker node sends request to other nodes to becone the leader, if all other nodes reply, the leader role is given
- After that, the leader sends message to other nodes that it is going to contimue the reader role
- If the nodes do not get a message in a particular time due to network issue or lead went down, again the random timer voting happens to select the leader node
- Each manager has its own copy of RAFT database and docker state database to be in sync
- If a change has to be made or applied, it needs to be notified to other manager nodes too and wait for quorum vaerification and then commit the changes to other master nodes's databases too
- Choose odd number of nodes in swarm because it increases the chances of the cluster to live when the cluster has to be divided into two segments
- By default manager nodea are worker too, we can make do not run any service and solely do management stuuff by setting `docker node update --availability drain NODE`

> **Quorum** is the number of members in a meeting to be available to make the proceedings of meeting valid.
> | MANAGERS | QUORUM |
> |----------|--------|
> | 3        | 2      |
> | 5        | 3      |
> | 7        | 4      |

> **WHY MULTIPLE NODES ?**
>Let us assume that one worker was to be added by to leader manager but the leader manager went down, the rest dont have info about the new worker and it is ignored in all the senerios even if it is there, this is called the distributed consensus.

> **WHAT HAPPENS IF MORE THAN HALF MANAGERS FAIL ?**
> We wont be able to do any managenent task but the current state of the swarm will continue to serve
> We should try to bring the managers online or we should force create a new cluster using `docker swarm init --force-new-cluster` to get a healthy new cluster with single manager

| COMMAND                                               | EFFECT                                                          |
|-------------------------------------------------------|-----------------------------------------------------------------|
| `docker swarm init --advertise-addr ADVERTISE_ADDR`   | Run on master node to initialize cluster                        |
| `docker swarm join --token JOIN_TOKEN IP`             | Join a swarm using the token                                    |
| `docker node update --availability AVAILABILITY NODE` | Set availability of node                                        |
| `docker swarm leave`                                  | Run on the node that should leave the swarm                     |
| `docker node rm NODE_ID`                              | Run on manager node to delist the removed node from the cluster |
| `docker swarm join-token manager`                     | Get join token for new manager nodes                            |
| `docker swarm join-token worker`                      | Get join token for new worker nodes                             |
| `docker node promote NODE_ID`                         | Promote a worker to a manager node                              |
| `docker node ls`                                      | List all nodes in docker swarm                                  |

### DOCKER SERVICE
* We can specify number of replicas
* Part of swarm orchestration
* We do not need to run images manually on all nodes
* If a image fails, the manager node detect it and creates a new one to repacee the failed one
* Services can be `replicated` and `golbal`, global mode is suitable for monitoring agent and logging agent or a caching service
* By default docker gives a funny name to all its containers but in case of swarm mode the names are like `NAME.1, NAME.2, NAME.3` this convention is for avoiding same name on the network and environment

| COMMAND                                                                                                                    | EFFECT                                                               |
|----------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| `docker service create --replicas=NUMBER -p HOST_PORT:CONTAINER_PORT -e VAR=VAL --network N1 N2 --name=SERVICE_NAME IMAGE` | Creates a service with replicas, port mapping and networks specified |
| `docker serivce update --replicas=NUMBER SERVICE_NAME`                                                                     | Scale a service                                                      |
| `docker service ls`                                                                                                        | List services that are active                                        |
| `docker service ps SERVICE_ID`                                                                                             | List active containers info for that service                         |
| `docker service update SERVICE_ID --publish-add HOST_PORT:CONTAINER_PORT`                                                  | Add a publishing port to service                                     |
| `docker service rm SERVICE_ID`                                                                                             | Remove a service                                                     |
| `docker network create --driver overlay --subnet SUBNET_CIDR NETWORK_NAME`                                                 | Create a overlay network, run on manager node                        |

### OVERLAY NETWORKING
* Allows containers to comminicate with each other over different hosts and work together
* When we create a swarm, it also creates a ingress network
> Ingress allows to accept connections from outside in a dynamic and configurable manner.
> Ingress network has a builtin load balancer that redirects traffic to published ports from all nodes to all nodes to all mapped ports on each container
* It also uses the embedded or builtin docker DNS

### STACKS
* Similar to services, but we dont have to run each service by hand, we can define a yaml file
* There are deploy parameters that we can define like `replicas`, `placement:constraints`, `resources` etc etc
* There a re mainly 3 levels `stack > service > container`

| COMMAND                                                   | EFFECT                                                          |
|-----------------------------------------------------------|-----------------------------------------------------------------|
| `docker stack deploy STACK_NAME --compose-file FILE_PATH` | Deploys services defined in compose file or update existing one |


### CI/CD
* CI/CD or CICD is the combined practices of continuous integration and continuous delivery or, less often, continuous deployment. They are sometimes referred to collectively as continuous development or continuous software development

* **STEPS**
    - Code repo has dockerfile
    - Buildsystems like GitHub Actions or Jenkins build image and tags it witha version number
    - Then the testing frameworks test the app and go for rigerous functionality testing in testing env
    - Then the image is published to docker registry or private registry

## DOCKER REGISTRY
* A central repository for docker containers
* Images are pulled from here by default
* We can also host our own private or public registry
* Deploying private docker registry
```
# DEPLOY
docker run -d -p 5000:5000 registry registry:2

# TAG IMAGE (with private registry url)
docker image tag IMAGE_NAME PRIVATE_REGISTRY_URL/IMAGE_NAME

# PUSH IMAGE
docker push PRIVATE_REGISTRY_URL/IMAGE_NAME
```

| COMMAND | EFFECT |
| ------- | ------ |
| `docker search IMAGE_NAME --limit 2 --filter stars=10 --filter is-official=true` | Search image in registry with output limit and filters to specify populatity and official image |

* Once an image has been built, we can push it to registry (docker hub or private)
* All images in self hosted registry are stored in `/var/lib/registry/docker/registry/vx/repositories`

> [!TIP]
> * Use `konradkleine/docker-registry-frontend:v2` for graphical registry management through browser

| COMMAND                                                                | EFFECT                      |
|------------------------------------------------------------------------|-----------------------------|
| `docker run -d -p 5000:5000 registry:2`                                | Host docker registry        |
| `docker build . -t ACCOUNT_CONTEXT_NAME/IMAGE_NAME:TAG`                | Build image and then tag it |
| `docker tag IMAGE_NAME REGISTRY_IP_HOSTNAME/IMAGE_NAME`                | Tag, retag esisting image   |
| `docker push REGISTRY_IP_HOSTNAME/ACCOUNT_CONTEXT_NAME/IMAGE_NAME:TAG` | Push the image to registry  |

> [!NOTE]
> * When we tag images, image do not get copied, but not two pointer point to the same image, we can see this when we see same hash id of images

> [!IMPORTANT]
> ### AUTHENTICATING TO A REGISTRY
> * Run `docker login REGISTRY_IP_HOSTNAME` and then enter credentials
> * After this we can run the push command to push the image to registry
