## DOCKER ENGINE ARCHITECTURE
* `Docker cli <-> REST API <-> Docker Daemon (Images, Volumes, Networks) <-> containerd (manage containers) <-> runC (run containers) <-> libcontainer <-> (namespaces and CGroups)`
* Docker initially used LXC containers but then replaced by libcontainer beaccause LXC was too dependent on kernel of host OS
* Open Container Initiative (OCI) fas formed by docker core team which laid guideline and stantards for containerization

* Docker objects:
    - Images
    - Containers
    - Networks
    - Volumes
* `Docker Registry` is a public registry for storing and fetching docker images, we also host our own registry

## DOCKER SERVICE CONFIGURATION
> [!TIP]
> Docker listend on a internal unix socket at `/var/run/docker.sock` or `unix:///var/run/docker.sock` for IPC

> [!IMPORTANT]
> Unix sockets are only accessable on same machine, they can not beaccessed from other machine

| COMMAND | EFFECT |
| ------- | ------ |
| `systemctl start docker` | Start docker service daemon |
| `dockerd --debug` | Run docker daemon in foreground debug mode in case it does not work properly |
| `dockerd --host=tcp://SELF_INTERFACE_IP:2375` | Allow docker daemon to listen on TCP port for commands from other machine |
| `dockerd --host=tcp://SELF_INTERFACE_IP:2376 --tls=true --tlscert=TLS_CERT_PATH --tlskey=TLS_KEY_PATH` | Securely listen on docker daemon |
| `export DOCKER_HOST="IP"` | Set IP of machine whose docker daemon we want to access, after doing this, all commands will run on the machine whose IP we provided |

> [!TIP]
> ### CONNECT TO OTHER MACHINE'S DOCKER DAEMON
> * First, allow that host machine having docker daemon to listen on tcp stream for commands
> * Export that machine's IP in terminal console where the docker CLI is installed
> * Now we can run all the docker commands and these will affect the docker daemon host machine

> [!CAUTION]
> * While giving access of docker daemon to external machine, be careful that we do not expose the daemon to public network if using in production as it do not require any authentication and does not have any encryption
> * For encrypted usage, expose the port `2376` instead of `2375`, also use the command to securely allow access to docker daemon and on the CLI client side use `export DOCKER_HOST="tcp://SELF_INTERFACE_IP:2376"`
>
> * All these parameters can be saved in a file at `/etc/docker/daemon.json` in the following way
> ```
> {
>     "debug": true,
>     "hosts": ["tcp://SELF_INTERFACE_IP:2376"]
>     "tls": true,
>     "tlscert": "TLS_CERT_PATH",
>     "tlskey": "TLS_KEY_PATH"
> } 
> ```
> Note that the `hosts` property is a array of multiple listeners

> [!TIP]
> * Sometimes a docker daemon may stop working and this brings all the containers down too, to avoid this and keep containers running even if daemon is not running add `"live-restore": true` in `/etc/docker/daemon.json`

> [!important]
> All image, container, networking related files are stored at `/var/lib/docker/`

> [!CAUTION]
> ## TROUBLESHOOT DOCKER DAEMON
> * Error: `Cannot connect to docker daemon at unix:///var/run/docker.sock`
> * If accessing docker daemon on remote machine, check for IP and ports and encryption parameters
> * Check `/etc/docker/daemon.json`
> * Check is free space is available on host: `df -h`
> * Prune all containers: `docker container prune`
> * Restart docker service: `systemctl status docker` and `systemctl start docker`
> * Add debug parameters in `/etc/docker/daemon.json` as `"debug": true` and reload docker service
> * Get info and logs after the daemon started: `docker system info` `docker system events`
> * Get more detailed logs: `tail -50 /var/log/messages`

## LOGGING DRIVERS
* Default logging driver for docker is `json-file`
* We can change the logging driver by either passing parameter and by editing `/etc/docker/daemon.json`
| COMMAND | EFFECT |
| ------- | ------ |
| `docker logs CONTAINER_ID` | See logs for container |
| `docker system info` | Get config |
| `cat /var/lib/docker/CONTAINER_ID` | See container logs in json format |

### CHANGE LOGGING DRIVER TO AWS
* Add the following lines to `/etc/docker/daemon.json`
```
"log-driver": "awslogs",
"log-opt": {
    "awslogs-region": "AWS_REGION"
}
```
* For these to work, we need to export AWS credentials, run the following in shell
```
export AWS_ACCESS_KEY_ID=KEY_ID
export AWS_SECRET_ACCESS_KEY=SECRET_KEY
export AWS_SESSION_TOKEN=SESSION_TOKEN
```
* We can specify driver for container while running it with `--log-driver DRIVER_NAME`
* We can also definee these properties in a compose file for each service

> [!TIP]
> ### CUSTOM LOGGING
> * For custom logging goto `docs.docker.com/config/containers/logging/` for customizing the default logging options
