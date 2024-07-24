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
> Docker listend on a internal unix socket at `/var/run/docker.sock` for IPC

> [!IMPORTANT]
> Unix sockets are only accessable on same machine, they can not beaccessed from other machine

| COMMAND | EFFECT |
| `systemctl start docker` | Start docker service daemon |
| `dockerd --debug` | Run docker daemon in foreground debug mode in case it does not work properly |
| `dockerd --host=tcp://SELF_INTERFACE_IP:2375` | Allow docker daemon to listen on TCP port for commands from other machine |
| `dockerd --host=tcp://SELF_INTERFACE_IP:2376 --tls=true --tlscert=TLS_CERT_PATH --tlskey=TLS_KEY_PATH`
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
