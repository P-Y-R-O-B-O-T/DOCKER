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

| COMMAND | EFFECT |
| ------- | ------ |
| `docker run IMAGE_NAME` | Run a container |
| `docker run -d IMAGE_NAME` | Run in detached mode |
| `docker run IMAGE_NAME [COMMANDS]` | Run a command in a container |
| `docker run -it IMAGE_NAME [COMMAND]` | Interactive pseudo terminal mode |
| `docker run -e VAR=VAL IMAGE` | Pass env var to container |
| `docker run --name=CONTAINER_NAME IMAGE_NAME` | Give name to container |
| `docker run -v VOLUME_NAME:/path/in/container` | Mount a volume |
| `docker run IMAGE --network=NETWORK_NAME` | Specify network to attach for container |
| `docker kill CONTAINER_ID` | Kill a container |
| `docker attach CONTAINER_ID` | Attach to a detached container |
| `docker ps` | List all running containers |
| `docker ps -a` | List all containers (even the stopped or exited ones) |
| `docker container stats` | See resource usage for all docker containers |
| `docker container top CONTAINER_ID` | See the processes and their behaviour in container |
| `docker container logs CONTAINER_ID` | Get container logs |
| `docker container logs -f CONTAINER_ID` | Watch realtime logs for containers |
| `docker container pause CONTAINER_ID` | Pauses the container in the same state |
| `docker container unpause CONTAINER_ID` | Resume a paused container |
| `docker container stop CONTAINER_ID` | Tries to kill the container gracefully, but if container does not quit, it force kills it (like but not SIGTERM, SIGKILL) |
| `docker container kill --signal=SIGNAL CONTAINER_ID` | We can kill docker containers like the normal kill command |
| `docker stop CONTAINER_ID` | Stop a container |
| `docker rm CONTAINER_ID` | Remove container |
| `docker images` | List all images |
| `docker inspect CONTAINER_ID` | Inspect container properties |
| `docker logs CONTAINER_ID` | Get logs for container |
| `docker rmi IMAGE_ID` | Remove image |
| `docker pull IMAGE_ID` | Pull image from remote repository |
| `docker exec CONTAINER_ID COMMANDS` | Execute a command in a running container |
| `docker build -t ACCOUNT_CONTEXT_NAME/IMAGE_NAME:TAG .` | Build a image with image and tag |
| `docker build . -f DOCKERFILE_NAME -t ACCOUNT_CONTEXT_NAME/IMAGE_NAME:TAG` | Build a image whose `Dockerfile` filename is different |
| `docker volume ls` | See available volumes |
| `docker volume create VOLUME_NAME` | Create a volume |
| `docker volume inspect VOLUME_ID` | Inspect a docker volume |
| `docker volume rm VOLUME_ID` | Remove a docker volume, but it must not be in use |
| `docker volume prune` | Remove all unused docker volumes and reclaim disk space |
| `docker network create --driver=DRIVER --subnet SUBNET_CIDR NETWORK_NAME` | Create a network |
| `docker nwtwork rm NETWORK_ID` | Remove a network |
| `docker network ls` | Show available networks |
| `docker network inspect NETWORK_ID` | Inspect a network |
| `docker network connect NETWORK_NAME CONTAINER_ID` | Attach a running container to a network |
| `docker network disconnect NETWORK_NAME CONTAINER` | Detach a running container from a network |
| `docker network prune` | Remove all unused docker network, but it does not remove default networks |
| `docker system df` | Show disk usage for docker images, containers and volumes |
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

### BASE VS PARENT IMAGES
* `SCRATCH -> PARENT_IMAGE -> BASE_IMAGE -> CUSTOM_IMAGE`
    - `PARENT_IMAGE` is generallly a OS image derived from SCRATCH
    - `BASE_IMAGE` is the images that we generally use like (python, php, node, etc)
    - `SCRATCH` is the default image in docker which consists of nothing

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
> #### COPY VS ADD
> * `ADD` directive have more features
>
> | DIRECTIVE COMMAND | EFFECT |
> | ----------------- | ------ |
> | `ADD LOCAL_TARFILE_PATH DESTINATION` | Copy and extract tar file |
> | `ADD REMOTE_TERFILE_PATH DESTINATION` | Copy and extract remote tar file |
>
> * `COPY` directive is pretty simple and just copies files and directories, we must use it generally
>
> * **SENARIO**
> * We have many steps even after copying and extracting the tar files, which increase build time after changes in file
> ```
> FROM IMAGE
> ADD REMOTE_TARFILE_PATH /DIRECTORY
> RUN tar -xJf /DIRECTORY/TARFILE -C /tmp/app
> RUN make -C /tmp/app
> ```
> * The file above can be refactored as below
> ```
> FROM IMAGE
> RUN CURL REMOTE_TARFILE_PATH | tar -xcJ /DIRECTORY/TARFILE && yarn build && rm /DIRECTORY/TARFILE
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

> [!IMPORTANT]
> ### MULTI STAGE BUILDS
> * **SENARIO**
> * We first build the project using a tool in server then copy that into a dockerfile and then ship, this becomes too complex and do not guarantees stability. But what if the build tool is different and the server that we run upon is different and thay are not found in the same image ? We can install the tool in the same production image but it will increase the image size and this wont be good in production environment.
> * The solution is MULTI STAGE BUILDS
> ```
> FROM IMAGE1 AS BUILD_STAGE_ID
>
> COPY . .
> RUN COMMAND
> RUN BUILD_COMMAND
>
> FROM IMAGE2
> COPY --from=BUILD_STAGE_ID FILE_SOURCE_ON_BUILD_STAGE_ID FILE_DESTINATION_ON_CURRENT_STAGE
> CMD ["COMMAND", "ARGUMENT"]
> ```
> * What we are seeing here is we build the project using different image and then import all the build files in the next build stage using the `COPY` directive and it uses a special parameter `--from=BUILD_STAGE_ID` which tells that from which build stage we need to copy the files and `BUILD_STAGE_ID` is defined using the `AS` directive in from `DIRECTIVE`
> * This saves time and space
>
> | COMMAND | EFFECT |
> | ------- | ------ |
> | `docker build -t IMAGE_NAME:TAG .` | All the stages are built and final stage image is tagged |
> | `docker build --target BUILD_STAGE_ID -t IMAGE_NAME:TAG .` | Sometimes we don't need to build all the stages, so we can specify from which build stage we should start building |
>
> * **ADVANTAGES**
>     - Optimize Dockerfiles
>     - No intermediate images
>     - Keeps image size low

### BUILD CACHE
* Each layer is cached while build process and the layers which are not required to rebuild are build from cache while re building process

> [!TIP]
> **SENARIO**
> * Suppose in a Dockerfile we are updating apt and then installing the packages and we are rebuilding after months and we have added an package and need pudated packages in rest of the packages, sisce updating apt repositories is cached and wont be rebuilt and repositories wont be updated
> * To solve this issue, repository updation and package installation should be part of a single instruction which will force the repositories to be updated 
> Example : `RUN apt-get update && apt-get install -y PACKAGE1 PACKAGE2`
> But this creates a build issue too because if we add or delete or modify a package, all packages will be installed again from scratch instead of caching and build will be taking time
> All instructions that change less frequently should be at top of the Dockerfile and the ones which are changed frequesntly at the bottom of the Dockerfile

### DOCKERFILE BEST PRACTICES
* Make it modular, solve specific task, do not make it generic
* Do not persist data inside a container, use a caching service or a object storage
* Keep image size small
* Maintain different images for different environments
* 

## DOCKER STORAGE
* `/var/lib/docker/` is the path for docker data like image data, container data etc
* Docker images are in layered arcitecture
* Copy-On-Write mechanish for layers
* Docker volume creates virtual buld sturage devices that can be used in the container
* We can directly bind files and directories onto docker containers, this is called bind mounts, here we do not create any volume
* By default, docker volumes have read and write properties but we can make them read only
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

* All containers goto default `bridge` network automatically, we can also force to attach to no network using `--network=none` while running container
* The `host` network take out any isolation between the docker host and docker container, it forces containers to attach to host network

> [!IMPORTANT]
> ### CONTAINER SEGMENTATION
> Note that a container can attach to multiple docker networks and it can communicate with only those containers that are on that network.
>
> If we have 3 networks `a`, `b` and `c` and 4 containers `v`, `x`, `y` and `z` and
>     - `a` contains `v`, `x`
>     - `b` contains `y`, `z`
>     - `c` contains `x`, `y`
> Then
>     - `v` can interact with `x` only
>     - `x` can interact with `v`, `y` only
>     - `y` can interact with `x`, `z` only
>     - `z` can interact with `y` only
>
> * We can create network segmentation and isolation between different services to enhance security
>     - External network named `public` for nginx containing only nginx containers for load balancing and SSL encryption
>     - Internal network named `internal` for web services containing web services containers and nginx containers for load balancing and SSL encryption
>     - Database network named `database` for web services and database containers and caching containers
>
> To access the containers, we can access IP addresses assigned to container, but this is not ideal as when container reboots it may get a different IP address, so we use container names. Docker has its own internal DNS that helps us do that, it creates namespaces for each container and for each service too

* Default CIDR for docker networks is `172.17.0.0/16`
* If we specify `--network=host` we can directly access the app on the host system on the same port that app is running in the container
* If we specify `--network=none` thwy do not get attached to any network
* All docker containers can resolve each other using the container names, docker has builtin DNS.

## DOCKER COMPOSE
* It is a way of defining the services and all of their properties in a single file
* The default version for compose specification is either `version: 2` or `version: 3`

## DOCKER SWARM
* **CONTAINER ORCHESTRATION**: A process of maintaining accessability security and reliability of multiple containers
* Combines multiple docker engines together into a single cluster for high availability and load balancing
* There are swarm managers and worker nodes
* Master node mantains the cluster state and manages the entire cluster, adding managing nodes, distributing services and responsibility
* There should be multiple manager nodes in odd numbers

> [!IMPORTANT]
>
> * ### HOW NODES INTERACT WITH EACH OTHER
>
> | PORT | DESCRIPTION |
> | ---- | ----------- |
> | TCP 2377 | Cluster management communications |
> | TCP and UDP 7946 | Communication among nodes |
> | UDP 4789 | Overlay network traffic |

* **Features**
    - Scale
    - Roll updates
    - Self Healing
    - Secure
    - Load balance
    - Service Discovery

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

| COMMAND | EFFECT |
| ------- | ------ |
| `docker swarm init --advertise-addr ADVERTISE_ADDR` | Run on master node to initialize cluster |
| `docker swarm join --token JOIN_TOKEN IP:PORT` | Join a swarm using the token, port is generally `2377` |
| `docker swarm init --autolock=true` | Init a cluster using with autolock |
| `docker swarm update --autolock=true` | Enable autolock in already running swarm |
| `docker node update --availability AVAILABILITY NODE` | Set availability of node |
| `docker swarm leave` | Run on the node that should leave the swarm |
| `docker node rm NODE_ID` | Run on manager node to delist the removed node from the cluster |
| `docker swarm join-token manager` | Get join token for new manager nodes |
| `docker swarm join-token worker` | Get join token for new worker nodes |
| `docker node promote NODE_ID` | Promote a worker to a manager node |
| `docker node demote NODE_ID` | Demote manager to worker node |
| `docker node ls` | List all nodes in docker swarm, `*` after a node means that we ran this command on that node |
| `docker node inspect NODE_ID --pretty` | See all info regarding the node |

> [!IMPORTANT]
> ### NODE AVAILABILITY
>     - `active`: The scheduler can assign new tasks to the node and the nodes can run previous tasks
>     - `pause`: The node can run previously assigned tasks but can't accept new tasks
>     - `drain`: The node can't accept new tasks and the tasks running are rescheduled on other node
> * We can drain a node when we want to do a maintainance work around that node
> ### MANAGER STATUS
> * It is available for only manager nodes
>     - Leader: The node is leader
>     - Reachable: The node is not leader and is healthy and working
>     - Unreachable: Ths node is unreachable and not functioning well

> [!IMPORTANT]
> ### REMOVING A NODE
> * Firstly drain the node
> * Then goto the node using ssh and run `docker swarm leave`
> * Finally run `docker node rm NODE_ID` on a manager node

> [!TIP]
> ### UPDATING and PATCHING
> * Before updating a node or patching it, set the node availability to `drain`
> * After doing all the good stuff, again set its availability to `active`

> [!TIP]
> ### DISTRIBUTED MANAGERS
> * Try to keep managers of different physical locations because if one site gode down, we still will have the rest of them
> * Try to have at least 3 sites

> [!IMPORTANT]
> ### WHAT HAPPENS IF MORE THAN HALF MANAGERS FAIL ?
>     - The services will continue to run but self healing and any kind of management operation will not take place
>     - We won't be able to do any managenent task but the current state of the swarm will continue to serve
>     - We should try to bring the managers online or we should force create a new cluster using `docker swarm init --force-new-cluster` to get a healthy new cluster with single manager

> [!TIP]
> ### AUTOLOCK
> * RAFT logs and docker nodes communication is protected using TLS keys, these keys are stored in docker manager's memory, but docker allows us to store it in external vaults too
> * If a node leaves and then requires to join back, it will need those keys if we have stored them in external vault
> * Sometimes we do not want the docker swarm to be access by anyone, so we auto lock it so that one more layer of security comes in hand
> * To do this we initialize the docker swarm using `--autolock=true` option, this returns a key that needs to be stored securely
> * not after restarting the docker daemon, we are unable to access the docker commands, docker is locked
> * To unlock it run `docker swarm unlock` and then enter the key
> * To enable autolock in a running swarm `docker swarm update --autolock=true`

### DOCKER SERVICE
* We can specify number of replicas
* Part of swarm orchestration
* We do not need to run images manually on all nodes
* If a image fails, the manager node detect it and creates a new one to repacee the failed one
* Services can be `replicated` and `golbal`, global mode is suitable for monitoring agent and logging agent or a caching service
* By default docker gives a funny name to all its containers but in case of swarm mode the names are like `NAME.1, NAME.2, NAME.3` this convention is for avoiding same name on the network and environment

| COMMAND | EFFECT |
| ------- | ------ |
| `docker service create --replicas=NUMBER -p HOST_PORT:CONTAINER_PORT -e VAR=VAL --network N1 N2 --name=SERVICE_NAME IMAGE` | Creates a service with replicas, port mapping and networks specified |
| `docker serivce update --replicas=NUMBER SERVICE_NAME` | Scale a service |
| `docker service update --image=IMAGE_NAME SERVICE_ID` | Update image for a service |
| `docker service ls` | List services that are active |
| `docker service ps SERVICE_ID` | List active containers info for that service |
| `docker service inspect SERVICE_ID --pretty` | Inspect a service |
| `docker service update SERVICE_ID --publish-add HOST_PORT:CONTAINER_PORT` | Add a publishing port to service |
| `docker service rm SERVICE_ID` | Remove a service |
| `docker network create --driver overlay --subnet SUBNET_CIDR NETWORK_NAME` | Create a overlay network, run on manager node |
| `docker service logs SERVICE_ID` | See service logs |

> [!TIP]
> #### ROLLING UPDATE
> * Sometimes we see that some image that has rolled out is not working properly, we can roll back to any version available in docker registry using `docker service update --image=IMAGE_NAME SERVICE_ID`
> * If it seema like images are being updated too frequently then we can update the delay between the updates using `docker service update --update-delay TIME SERVICE_ID`
> * If we need to update multiple replicas at same time `docker service update --update-parallelism N SERVICE_ID`
> * If a rollback fails we have to take an action out of three options `pause`, `continue` and `rollback`, we can do so using `docker service update --update-failure-action ACTION SERVICE_NAME`
> * To do a general roll back `docker service update --rollback SERVICE_ID`

> [!IMPORTANT]
> #### GLOBAL SERVICE
> * Defaultmode for services is `replicated`
> * Global mode ensures only one instance per node
> * Generally used for monitoring agents
> * Create one using `docker service create --mode=global --image=IMAGE_NAME --name=SERVICE_NAME` or update using `docker service update --mode=global SERVICE_ID`

> [!TIP]
> #### LABEL and CONSTRAINTS
> * Many time multiple type resourced nodes are there in swarm and we need to run specific type of service on specific type of node
> * We add labels to the nodes `docker node update --label-add LABEL_KEY=LABEL_VALUE NODE_ID`
> * After this, we need to set a constraint in service `docker service create --constraint=node.labels.LABEL_KEY=LABEL_VALUE --name=SERVICE_NAME --image=IMAGE_NAME`, we can also use the `!=` for node label comparison

### OVERLAY NETWORKING
* Allows containers to comminicate with each other over different hosts and work together
* When we create a swarm, it also creates a ingress network which is the default network for overlay networks

> [!IMPORTANT]
> #### OVERLAY MECHANISM
> * Ingress allows to accept connections from outside in a dynamic and configurable manner.
> * Ingress network has a builtin load balancer that redirects traffic to published ports from all nodes to all nodes to all mapped ports on each container
> * It also uses the embedded or builtin docker DNS

> [!TIP]
> #### CREATE OVERLAY NETWORK
> * We can create a overlay networks using `--driver overlay` option while creating the network
> * We can enable application data encryption by using `--opt encrypted` while creating the network

> [!TIP]
> ### MACVLANS
> * These are used for legacy applications that assume to be connected to a physical network
> * We define a macvlan network which assigns a mac address to each container's vitrual interface
> * `docker network create --driver mcvlan -o parent=INTERFACE NETWORK_NAME`, interface is the name of device that we use to transmit data, see `ip a` command and `ifconfi too`

> [!TIP]
> ### CONFIG OBJECTS
> * We have a config on the manager and we run a service on global mode or replicated mode, the service need to be on every machine, not just one
> * We create config on every node using `docker config create PATH_TO_CONFIG_ALL_NODES PATH_TO_CONFIG_CURRENT_NODE`
> * This create a copy of config file on each node by taking second file path as source
> * We can set an config file as to container path `docker service create --config src=nginx-conf,target="PATH_ON_CONTAINER" SERVICE_NAME`
> * We can also remove the config file from particular service by `docker service update --config-rm PATH_TO_CONFIG_ALL_NODES SERVICE_ID`

> [!IMPORTANT]
> ### SERVICE DISCOVERY
> * All of the containers of a service can be resolved using service name or container name
> * This works only on a user created network, not on the default ones

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
