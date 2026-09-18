# Docker Cheat Sheet

## 🚢 Installation

On Linux, install Docker Engine: https://docs.docker.com/engine/install/
On Windows or macOS, install Docker Desktop: https://docs.docker.com/desktop/
Run this command to verify your installation: docker run hello-world

## 📖 Container commands

Command | Description
--------|-------------
docker run \<image>                         | Create and run a new container
docker run -p 8080:80 \<image>              | Publish container port 80 to host port 8080
docker run -d \<image>                      | Run a container in the background (detached)
docker run -v \<host>:\<path> \<image>      | Mount a host directory to a path in the container
docker run -v \<host>:\<path>:ro \<image>   | Mount the directory as a readonly volume
docker ps                                   | List running containers
docker ps -a                                | List all containers (running or stopped)
docker stats                                | Display a live stream of resource usage statistics
docker logs -f \<container_name>            | Follow the log stream of a container
docker stop \<container_name>               | Stop a running container
docker start \<container_name>              | Start a stopped container
docker rm \<container_name>                 | Remove a container

## 🔌 Executing commands in a container

Command | Description
--------|-------------
docker exec \<container_name> \<command>  | Execute a command in a running container
docker exec -it \<container_name> bash    | Open an interactive shell in a running container

## 🧩 Image commands

Command | Description
--------|-------------
docker build -t \<image_name> .       | Build and tag a new image from a Dockerfile
docker build -f \<file> .             | Build with Name of the Dockerfile 
docker build --target=\<stage> .      | for multistage files, build up to the target stage 
docker history \<image>               | Show the image layers
docker images                         | List local images
docker rmi \<image>                   | Remove an image
docker image prune                    | Remove all unused images

## ☁️ Container registry commands

Command | Description
--------|-------------
docker login -u \<user-name>                                        | Login to Docker Hub
docker login \<server>                                              | Login to another container registry
docker logout                                                       | Logout of Docker Hub
docker logout \<server>                                             | Logout of another container registry
docker push \<image>                                                | Upload an image to a registry
docker pull \<image>                                                | Download an image from a registry
docker search \<image>                                              | Search Docker Hub for images
docker tag \<source_image>[:tag] \<target_image>[:tag]              | Add new name or alias
docker tag \<source_image>[:tag] registry/username/repository[:tag] | Tag registry

> Docker Image Naming Convention:
[registry/][username/]repository[:tag]   
  >> "[]" are optional  
  default registry is docker.io


## 🛠️ System commands

Command | Description
--------|-------------
docker system df                | Show Docker disk usage
docker system prune             | Remove unused data
docker system prune -a          | Remove all unused data
docker info                     | Display system-wide information

## 🔧 Dockerfile instructions

Instruction | Description
------------|-------------
FROM \<image>                       | Set the base image
FROM \<image> AS \<name>            | Set the base image and name the build stage
RUN \<command>                      | Execute a command as part of the build process
CMD \<command>                      | Execute a command when the container starts
ENTRYPOINT \<command>               | Configure the container to run as an executable
ENV \<key>=\<value>                 | Set an environment variable
EXPOSE \<port>                      | Expose a port
COPY \<src> \<dest>                 | Copy files from source to destination
COPY --from=\<name> \<src> \<dest>  | Copy files from a build stage to destination
ADD \<src> \<dest>                  | Like Copy but can fetch from url and can auto decompress(.tar, .tgz,...)
WORKDIR \<path>                     | Set the working directory
VOLUME \<container_path>            | Create a mount point, in build give a randome name to docker volume
USER \<user>                        | Set the user
ARG \<name>                         | Define a build argument
ARG \<name>=\<default>              | Define a build argument with a default value
LABEL \<key>=\<value>               | Set a metadata label
HEALTHCHECK \<command>              | Set a healthcheck command

## Docker Network Modes

Driver | Description
--------|------------
bridge  |	The default network driver.
host	  | Remove network isolation between the container and the Docker host.
none	  | Completely isolate a container from the host and other containers.
overlay	| Swarm Overlay networks connect multiple Docker daemons together.
ipvlan	| Connect containers to external VLANs.
macvlan	| Containers appear as devices on the host's network.

| Driver      | When to use|
| ----------- | ---------- |
| **bridge**  | **Default choice** for containers running on the **same Docker host**. Containers communicate with each other through a virtual network. Good for most Docker Compose applications.                                            |
| **host**    | Use when the container should **share the host's network stack** directly. No separate container IP/port mapping. Useful when maximum network performance or direct access to host interfaces is important.                    |
| **overlay** | Use for **communication between containers on multiple Docker hosts**, typically with **Docker Swarm**. Creates a virtual network spanning multiple machines.                                                                  |
| **ipvlan**  | Use when containers need to appear directly on the **physical network** with their own IP addresses, while sharing the host's network interface. Useful when you need efficient L2/L3 networking and many container endpoints. |
| **macvlan** | Use when containers need their **own MAC addresses** and should appear as physical devices on the LAN. Useful for legacy applications or network appliances that require direct Layer-2 presence.                              |

| Command      | Description|
| ----------- | ---------- |
| docker network ls                                         | List of networks      |
| docker network create                                     | Create new network    | 
| docker network inspect \<network_name>                    | inspect the network   |
| docker network connect \<network_name> \<container_name>  | inspect the network   |

> use `sudo iptables -t nat -L -n` to show NAT config in port mapped containers   
> use for example `docker run -d --network host ubuntu` for connect to host network 

## Docker Volumes

### commands
| Command      | Description|
| ----------- | ---------- |
| docker volume create /<volume_name>     | Create new volume     |
| docker volume ls                        | Show list of volumes  |
| docker volume inspect /<volume_name>    | Inspect the volume    |
| docker volume rm /<volume_name>         | Delete the volume     |

- docker volumes path: /var/lib/docker/volume/<volume_name>/_data

### Practical rule:  
| Requirement                                   | Recommended                                  |
| --------------------------------------------- | -------------------------------------------- |
| Database persistent data                      | **Named volume**                             |
| Application-generated persistent data         | **Named volume**                             |
| Configuration from host                       | **Bind mount, `:ro`**                        |
| Source code during development                | **Bind mount**                               |
| Logs that must be directly accessible on host | **Bind mount**                               |
| Temporary data                                | **tmpfs**                                    |
| Production application data                   | Usually **named volume** or external storage |

### The bind mount issue:

> A bind mount hides the entire target directory that was created in the image.   

For example, suppose your Dockerfile has:
```dockerfile
RUN mkdir -p /app/config
COPY config/default.yaml /app/config/
```

and then you run:
```yaml
volumes:
  - ./config:/app/config
```
Docker does not merge the directories. The result inside the running container is contents of ./config

Best Practice:
- Mount only a specific file not entire directory
- Use a separate directory in path of the data
- Use Named volume
- always check the image content

**Named volume behavior:**
- if the volume is empty, docker copies the content of image to the volume.
- if the volume has content, the volume content replace(overwtites) with the image content.

## 📝 Docker Compose

Command | Description
--------|-------------
docker compose up -d                    | Create and start containers in docker-compose.yaml directory
docker compose up --build               | Rebuild images before starting containers
docker compose run /<service> [COMMAND] | Run a service in docker compose
docker compose stop                     | Stop services
docker compose down                     | Stop and remove containers and networks
docker compose ps                       | List running containers
docker compose logs                     | View the logs of all containers
docker compose logs \<service>          | View the logs of a specific service
docker compose logs -f                  | View and follow the logs

### 📌 Docker Compose file reference

Key      | Description
---------|-------------
project_name                         | Set the name of the project, not base directory
services.\<name>.image               | Set the image to use or build
services.\<name>.container_name      | Set the container name
services.\<name>.hostname            | Set hostname for the container. Later, another container can ping with it
services.\<name>.build               | Build context and options
services.\<name>.build.context       | Build context (default is the current directory)
services.\<name>.build.dockerfile    | Dockerfile to use (default is Dockerfile)
services.\<name>.build.target        | Build stage to use(for multistage)
services.\<name>.build.args          | Build arguments
services.\<name>.command             | Override the default command for the container
services.\<name>.entrypoint          | Override the default entrypoint for the container
services.\<name>.volumes             | Mount volumes in the container
services.\<name>.volumes.type        | 'bind', 'volume'
services.\<name>.volumes.source      | source path
services.\<name>.volumes.target      | container path
services.\<name>.volumes.read_only   | true, false
services.\<name>.ports               | Publish container ports to the host
services.\<name>.environment         | Set environment variables in the container
services.\<name>.env_file            | Set environment file in the container
services.\<name>.restart             | Restart policy (no/always/on-failure/unless-stopped)
services.\<name>.scale               | Set the number of containers to run
services.\<name>.networks            | List of networks to connect the container to
services.\<name>.depends_on          | List of services to start before this service
services.\<name>.labels              | Set metadata labels for the container
networks                             | A list of networks defined in the file
networks.\<net_name>.driver          | Set the network driver
networks.\<net_name>.external        | Don't create the network, use an existing one
volumes                              | A list of volumes defined in the file
volumes.\<vol_name>.name             | Set the name of the volume
volumes.\<vol_name>.driver           | Set the volume driver
configs                              | A list of configs defined in the file
secrets                              | A list of secrets defined in the file
include.path                         | Include sub compose.yaml

### diffrence between `$` and `$$` in compose.yam and Dockerfile.yaml:
when there is environment variables.
| Context         | `$VAR`| `$$VAR`|
| --------------- | ----- | ------ |
| **Compose YAML**| Compose substitutes the variable | Escapes `$` so the container receives `$VAR` |
| **Dockerfile (`RUN`)** | Expanded by the shell (or Docker for some instructions) | `$$` is the shell's PID, **not** an escape for `$` |


### 🔄 Docker compose hot reload file
When something changes, Docker automatically performs an action. no need to down, rebuild, up.
Its useful for development application. Its excelent for script langages like Python, Nodejs, PHP, Frontend... not for compilation ones like Go.

`docker compose up --watch`

```YAML
## compose.yaml:
develop:
  watch:
    - action: sync
      path: ./src
      target: /app/src
```

### ✅ Docker compose health check
```YAML
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```
| Option      |	Purpose  |
|-------------|----------                                                 |
| test        | Command executed to check service health                  |
| interval    |	Time between successive health checks                     |
| timeout	    | Maximum time to wait for a check to complete              |
| retries	    | Failures required before marking the container unhealthy	|
|start_period	| Ignoring period before counting failures                  |

see this: https://last9.io/blog/docker-compose-health-checks/

## ⛩️ Dockerfile package manager update/install
```bash
## Ubuntu
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    && rm -rf /var/lib/apt/lists/*

## Alpine
RUN apk add --no-cache curl
```

## 🗂️ multiple compose file

```YAML
# main-compose.yaml
include:
  - ./database/compose.yaml
  - ./cache/redis.compose.yaml
  - oci://docker.io/team/analytics:latest # You can even include from remote sources like git

services:
  webapp:
    build: .
    depends_on:
      - database  # This service is defined in the included database/compose.yaml
      - redis     # This service is defined in the included redis.compose.yaml
```      

 if an included file defines a service, network, or volume that has the same name as one in the main file, the merge will fail to prevent accidental overrides. You can intentionally override these settings by using a standard compose.override.yaml file. This file automatically merged after main compose.yaml and can override configurations from the included files.
