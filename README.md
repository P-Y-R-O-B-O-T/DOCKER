# DOCKER

## INTRO
* Docker is a set of platform as a service products that use OS-level virtualization to deliver software in packages called containers
### PROBLEMS FACED
- Compatibility with os, between apps (matrix from hell)
- long setup time
### SOLUTION
- **Containers**
    - Isolated runtime environment (LXC, LXD, LXCFS), Docker uses LXC
    - Helpt to package, containerize and ship applications
    - They are lightweight and less isolated than dedicated VM
    - Docker uses kernel of ubderlying host (kernel sharing)
- Hypervisor: Daemon which runs and maintain multiple VMs

## DOCKER COMMANDS
| COMMAND                                                                   | EFFECT                                                |
|---------------------------------------------------------------------------|-------------------------------------------------------|
| `docker run IMAGE_NAME`                                                   | Run a container                                       |
| `docker run -d IMAGE_NAME`                                                | Run in detached mode                                  |
| `docker run IMAGE_NAME [COMMANDS]`                                        | Run a command in a container                          |
| `docker run -it IMAGE_NAME [COMMAND]`                                     | Interactive pseudo terminal mode                      |
| `docker run -e VAR=VAL IMAGE`                                             | Pass env var to container                             |
| `docker run --name=CONTAINER_NAME IMAGE_NAME`                             | Give name to container                                |
| `docker run -v VOLUME_NAME:/path/in/container`                            | Mount a volume                                        |
| `docker run IMAGE --network=NETWORK_NAME`                                 | Specify network to attach for container               |
| `docker kill CONTAINER_ID`                                                | Kill a container                                      |
| `docker kill $(docker ps -aq)`                                            | Kill all containers                                   |
| `docker attach CONTAINER_ID`                                              | Attach to a detached container                        |
| `docker ps`                                                               | List all running containers                           |
| `docker ps -a`                                                            | List all containers (even the stopped or exited ones) |
| `docker stop CONTAINER_ID`                                                | Stop a container                                      |
| `docker rm CONTAINER_ID`                                                  | Remove container                                      |
| `docker rm $(docker ps -aq)`                                              | Remove all containers                                 |
| `docker images`                                                           | List all images                                       |
| `docker inspect CONTAINER_ID`                                             | Inspect container properties                          |
| `docker logs CONTAINER_ID`                                                | Get logs for container                                |
| `docker rmi IMAGE_ID`                                                     | Remove image                                          |
| `docker pull IMAGE_ID`                                                    | Pull image from remote repository                     |
| `docker exec CONTAINER_ID COMMANDS`                                       | Execute a command in a running container              |
| `docker build -t repo/image:tag .`                                        | Build a image with image and tag                      |
| `docker volume create VOLUME_NAME`                                        | Create a volume                                       |
| `docker network create --driver=DRIVER --subnet SUBNET_CIDR NETWORK_NAME` | Create a network                                      |
| `docker network ls`                                                       | Show available networks                               |

* Port mapping: HOST_PORT:CONTAINER_PORT
* Docker volume

## DOCKER IMAGES
* Creation: `Dockerfile`
* `Instruction` `Argument` format of file
* All capitalized words are instructions, rest on the right are arguments
* Layered architecture: each instruction line creates a new layer with just the changes from the previous layer
* **CMD VS ENTRYPOINT**
- The parameters we specify in run command get totally replaced in `CMD`
- The parematers we specify in run command get appended to the `ENTRYPOINT` specified in the `Dockerfile`
- Also we get a flexibility to execute containers with dynamic variables through the run command while using `ENTRYPOINT`
```
EXAMPLE
# CMD
RUN ["sleep", "10"]
docker run image sleep 10

# ENTRYPOINT
ENTRYPOINT ["sleep"]
docker run image 10
```
- Setting default parameters for all the variables in imp to avoid mishaps we can do the following by:
```
ENTRYPOINT ["sleep"]
CMD ["5"]
```

## DOCKER COMPOSE

## DOCKER ENGINE
* Docker engine is considered as host with docker installed on it
* `Docker CLI <-> REST API <-> Docker Deamon`
* We can access remote docker like docker -H=IP_HOSTNAME:2375 DOCKER_COMMAND
### UNDER THE HOOD
* Docker uses namespaces for isolation: process ID, network, mount, IPC, Unix timesharing
* A single process can have multiple process IDs, one for host and one for container
* All processes are running on host but seperated with namespaces and scopes
* We can also specify amount of resources that a container can use, this is implemented using `cgroups`

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

## DOCKER NETWORKING
* Default networks are: `bridge`, `none` and `host`
* Default CIDR for docker networks is `172.17.0.0/16`
* If we specify `--network=host` we can directly access the app on the host system on the same port that app is running in the container
* If we specify `--network=none` thwy do not get attached to any network
* All docker containers can resolve each other using the container names, docker has builtin DNS.

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

## CONTAINER ORCHESTRATION
* A process of maintaining accessability security and reliability of multiple containers

## DOCKER SWARM
* Combines multiple docker engines together into a single cluster for high availability and load balancing
* There are swarm managers and worker nodes
