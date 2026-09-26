# Docker Cheat Sheet

## 🚢 Installation

- Linux: install Docker Engine from https://docs.docker.com/engine/install/
- Windows or macOS: install Docker Desktop from https://docs.docker.com/desktop/
- Verify the installation with:

```bash
docker run hello-world
```

---

## 📖 Container commands

| Command | Description |
| ------- | ----------- |
| `docker run <image>` | Create and run a new container |
| `docker run -p 8080:80 <image>` | Publish container port 80 to the host port 8080 |
| `docker run -d <image>` | Run a container in the background (detached mode) |
| `docker run -v <host>:<path> <image>` | Mount a host directory to a path inside the container |
| `docker run -v <host>:<path>:ro <image>` | Mount the directory as a read-only volume |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers, including stopped ones |
| `docker stats` | Display live resource usage statistics |
| `docker logs -f <container_name>` | Follow the log stream of a container |
| `docker stop <container_name>` | Stop a running container |
| `docker start <container_name>` | Start a stopped container |
| `docker rm <container_name>` | Remove a container |

---

## 🔌 Executing commands in a container

| Command | Description |
| ------- | ----------- |
| `docker exec <container_name> <command>` | Execute a command in a running container |
| `docker exec -it <container_name> bash` | Open an interactive shell in a running container |

---

## 🧩 Image commands

| Command | Description |
| ------- | ----------- |
| `docker build -t <image_name> .` | Build and tag a new image from a Dockerfile |
| `docker build -f <file> .` | Build using a specific Dockerfile |
| `docker build --target=<stage> .` | For multi-stage builds, build up to the target stage |
| `docker history <image>` | Show the image layers |
| `docker images` | List local images |
| `docker rmi <image>` | Remove an image |
| `docker image prune` | Remove all unused images |

---

## ☁️ Container registry commands

| Command | Description |
| ------- | ----------- |
| `docker login -u <user-name>` | Log in to Docker Hub |
| `docker login <server>` | Log in to another container registry |
| `docker logout` | Log out of Docker Hub |
| `docker logout <server>` | Log out of another container registry |
| `docker push <image>` | Upload an image to a registry |
| `docker pull <image>` | Download an image from a registry |
| `docker search <image>` | Search Docker Hub for images |
| `docker tag <source_image>[:tag] <target_image>[:tag]` | Add a new name or alias |
| `docker tag <source_image>[:tag] registry/username/repository[:tag]` | Tag for a registry |

> Docker image naming convention:
>
> `[registry/][username/]repository[:tag]`
>
> `[]` indicates optional parts.
> The default registry is `docker.io`.

---

## 🛠️ System commands

| Command | Description |
| ------- | ----------- |
| `docker system df` | Show Docker disk usage |
| `docker system prune` | Remove unused data |
| `docker system prune -a` | Remove all unused data |
| `docker info` | Display system-wide information |

---

## 🔧 Dockerfile instructions

| Instruction | Description |
| ----------- | ----------- |
| `FROM <image>` | Set the base image |
| `FROM <image> AS <name>` | Set the base image and name the build stage |
| `RUN <command>` | Execute a command as part of the build process |
| `CMD <command>` | Run a command when the container starts |
| `ENTRYPOINT <command>` | Configure the container to run as an executable |
| `ENV <key>=<value>` | Set an environment variable |
| `EXPOSE <port>` | Expose a port |
| `COPY <src> <dest>` | Copy files from source to destination |
| `COPY --from=<name> <src> <dest>` | Copy files from a build stage to the destination |
| `ADD <src> <dest>` | Similar to `COPY`, but can fetch from a URL and auto-decompress archives like `.tar` and `.tgz` |
| `WORKDIR <path>` | Set the working directory |
| `VOLUME <container_path>` | Create a mount point; in a build, give the Docker volume a random name |
| `USER <user>` | Set the user |
| `ARG <name>` | Define a build argument |
| `ARG <name>=<default>` | Define a build argument with a default value |
| `LABEL <key>=<value>` | Set metadata labels |
| `HEALTHCHECK <command>` | Define a health check command |

### MultiStage best practice
When to use multi-stage builds

- Compiled languages (Go, Rust, C/C++, Java) — you need a compiler/SDK to build, but not to run the binary
- Frontend apps (React, Vue, Angular) — you need Node + build tools to bundle, but the output is just static files served by nginx
- Any language with a build step — TypeScript, Sass, etc.
- Reducing attack surface — fewer packages in the final image means fewer CVEs
- Separating test stages — run tests in an intermediate stage without shipping test dependencies

**Best practices:**

1. Use minimal final-stage base images
    - distroless, alpine, or scratch (for static binaries) instead of full OS images
    - scratch works great for Go/Rust static binaries — literally zero extra surface
2. Order the Docker instructions from the least to most likely to change
3. Run as non-root in the final stage
    ```Dockerfile
    RUN adduser -D appuser
    USER appuser
    ````
4. Avoid latest image versions
5. Combine RUN commands to minimize layers within a stage
    ```bash
    RUN apt-get update && apt-get install -y --no-install-recommends \
    package1 package2 \
    && rm -rf /var/lib/apt/lists/*
    ```
6. Use .dockerignore
    - Keep node_modules, .git, build tools, etc. out of the build context — speeds up builds and avoids accidental copies.
7. use Dockerfile stages for build-time variation (what's in the image), and docker-compose for run-time variation (how the container behaves — ports, volumes, env, bind mounte source code).
---

## 🌐 Docker network modes

| Driver | Description |
| ------ | ----------- |
| `bridge` | Default network driver. Containers communicate through a virtual network on the same Docker host. |
| `host` | Removes network isolation between the container and the Docker host. |
| `none` | Completely isolates the container from the host and other containers. |
| `overlay` | Connects multiple Docker daemons together in a Swarm overlay network. |
| `ipvlan` | Connects containers directly to external VLANs. |
| `macvlan` | Makes containers appear as devices on the host network. |

| Driver | When to use |
| ------ | ----------- |
| **bridge** | Default choice for containers running on the same Docker host. Good for most Docker Compose applications. |
| **host** | Use when the container should share the host network stack directly. Suitable for maximum performance or direct access to host interfaces. |
| **overlay** | Use for communication between containers on multiple Docker hosts, typically with Docker Swarm. |
| **ipvlan** | Use when containers need their own IP addresses on the physical network while sharing the host interface. |
| **macvlan** | Use when containers need their own MAC addresses and should appear as physical devices on the LAN. |

| Command | Description |
| ------- | ----------- |
| `docker network ls` | List networks |
| `docker network create` | Create a new network |
| `docker network inspect <network_name>` | Inspect a network |
| `docker network connect <network_name> <container_name>` | Connect a container to a network |

> Use `sudo iptables -t nat -L -n` to view NAT rules for port-mapped containers.
>
> Example: `docker run -d --network host ubuntu` to connect a container to the host network.

---

## 💾 Docker volumes

### Commands

| Command | Description |
| ------- | ----------- |
| `docker volume create <volume_name>` | Create a new volume |
| `docker volume ls` | List volumes |
| `docker volume inspect <volume_name>` | Inspect a volume |
| `docker volume rm <volume_name>` | Delete a volume |

- Volume path on Linux: `/var/lib/docker/volumes/<volume_name>/_data`

### Practical rules

| Requirement | Recommended |
| ----------- | ----------- |
| Database persistent data | **Named volume** |
| Application-generated persistent data | **Named volume** |
| Configuration from host | **Bind mount with `:ro`** |
| Source code during development | **Bind mount** |
| Logs that must be directly accessible on the host | **Bind mount** |
| Temporary data | **tmpfs** |
| Production application data | Usually **named volume** or external storage |

### Bind mount caveat

> A bind mount hides the entire target directory that was created in the image.

Example:

```dockerfile
RUN mkdir -p /app/config
COPY config/default.yaml /app/config/
```

Then run:

```yaml
volumes:
  - ./config:/app/config
```

Docker does not merge the directories. The result inside the running container is the contents of `./config` only.

### Best practices

- Mount only a specific file instead of an entire directory when possible.
- Use a separate directory for data storage.
- Prefer named volumes for persistent data.
- Always verify the image contents before mounting.

**Named volume behavior:**

- If the volume is empty, Docker copies the contents of the image into the volume.
- If the volume already contains data, the existing volume content is preserved and replaces the image content behaviorally when mounted.

---

## 📝 Docker Compose

| Command | Description |
| ------- | ----------- |
| `docker compose up -d` | Create and start containers in the current `docker-compose.yaml` directory |
| `docker compose up --build` | Rebuild images before starting containers |
| `docker compose run <service> [COMMAND]` | Run a service in Docker Compose |
| `docker compose stop` | Stop services |
| `docker compose down` | Stop and remove containers, networks, and related resources |
| `docker compose ps` | List running containers |
| `docker compose logs` | View logs for all services |
| `docker compose logs <service>` | View logs for a specific service |
| `docker compose logs -f` | View and follow logs |

### Docker Compose file reference

| Key | Description |
| --- | ----------- |
| `project_name` | Set the project name, not the base directory |
| `services.<name>.image` | Set the image to use or build |
| `services.<name>.container_name` | Set the container name |
| `services.<name>.hostname` | Set the hostname of the container |
| `services.<name>.build` | Build context and options |
| `services.<name>.build.context` | Build context (default: current directory) |
| `services.<name>.build.dockerfile` | Dockerfile to use (default: `Dockerfile`) |
| `services.<name>.build.target` | Build stage to use for multi-stage builds |
| `services.<name>.build.args` | Build arguments |
| `services.<name>.command` | Override the default container command |
| `services.<name>.entrypoint` | Override the default entrypoint |
| `services.<name>.volumes` | Mount volumes inside the container |
| `services.<name>.volumes.type` | `bind` or `volume` |
| `services.<name>.volumes.source` | Source path |
| `services.<name>.volumes.target` | Container path |
| `services.<name>.volumes.read_only` | `true` / `false` |
| `services.<name>.ports` | Publish container ports to the host |
| `services.<name>.environment` | Set environment variables inside the container |
| `services.<name>.env_file` | Set environment files |
| `services.<name>.restart` | Restart policy (`no`, `always`, `on-failure`, `unless-stopped`) |
| `services.<name>.scale` | Set the number of containers to run |
| `services.<name>.networks` | Networks to connect the container to |
| `services.<name>.depends_on` | Services to start before this service |
| `services.<name>.labels` | Set metadata labels |
| `networks` | List of networks defined in the file |
| `networks.<net_name>.driver` | Set the network driver |
| `networks.<net_name>.external` | Reuse an existing network instead of creating a new one |
| `volumes` | List of volumes defined in the file |
| `volumes.<vol_name>.name` | Set the volume name |
| `volumes.<vol_name>.driver` | Set the volume driver |
| `configs` | List of configs defined in the file |
| `secrets` | List of secrets defined in the file |
| `include.path` | Include sub-compose files |

### Difference between `$` and `$$` in Compose and Dockerfile

When environment variables are used:

| Context | `$VAR` | `$$VAR` |
| ------- | ----- | ------ |
| **Compose YAML** | Compose substitutes the variable | Escapes `$` so the container receives `$VAR` |
| **Dockerfile (`RUN`)** | Expanded by the shell (or Docker for some instructions) | `$$` is the shell PID and is not an escape for `$` |

---

## 🔄 Docker Compose hot reload

When a file changes, Docker can automatically perform an action. This is useful during development and avoids the need to stop, rebuild, and restart manually.

It is especially helpful for scripting languages such as Python, Node.js, PHP, and frontend frameworks, but less so for compiled languages such as Go.

```bash
docker compose up --watch
```

```yaml
# compose.yaml
services:
  app:
    build: .
    develop:
      watch:
        - action: sync
          path: ./src
          target: /app/src
```

---

## ✅ Docker Compose health check

```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 30s
  timeout: 10s
  retries: 3
  start_period: 40s
```

| Option | Purpose |
| ------ | ------- |
| `test` | Command used to check service health |
| `interval` | Time between checks |
| `timeout` | Maximum time allowed for a check to complete |
| `retries` | Number of failed checks before the container is marked unhealthy |
| `start_period` | Grace period before failures are counted |

See: https://last9.io/blog/docker-compose-health-checks/

---

## ⛩️ Dockerfile package manager update and install

```bash
# Ubuntu
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Alpine
RUN apk add --no-cache curl
```

---

## 🗂️ Multiple Compose files

```yaml
# main-compose.yaml
include:
  - ./database/compose.yaml
  - ./cache/redis.compose.yaml
  - oci://docker.io/team/analytics:latest # You can include remote sources as well

services:
  webapp:
    build: .
    depends_on:
      - database  # defined in ./database/compose.yaml
      - redis     # defined in ./cache/redis.compose.yaml
```

If an included file defines a service, network, or volume with the same name as one in the main file, the merge fails to prevent accidental overrides. You can intentionally override these settings by using a standard `compose.override.yaml` file, which is automatically merged after the main compose file and can override included configurations.
