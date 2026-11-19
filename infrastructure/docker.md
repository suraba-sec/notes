# Docker — Fundamentals and Practical Reference

**Level:** Beginner  
**Last reviewed:** 2026-11-19  
**Purpose:** Personal reference for learning Docker and using it in development, infrastructure, and DevOps workflows.

---

## 1. TL;DR

Docker is a platform for packaging and running applications in isolated environments called **containers**.

Key concepts:

- **Image:** A read-only template used to create containers.
- **Container:** A running or stopped instance of an image.
- **Dockerfile:** Instructions for building an image.
- **Volume:** Persistent storage managed by Docker.
- **Network:** Allows containers to communicate.
- **Docker Compose:** Defines and manages multi-container applications.

Typical workflow:

1. Write application code.
2. Create a `Dockerfile`.
3. Build an image.
4. Run a container.
5. Check logs and troubleshoot.
6. Use Docker Compose when multiple services are needed.

---

## 2. What problem does Docker solve?

Applications depend on more than their source code. They may require:

- A specific programming language version.
- Libraries and dependencies.
- Environment variables.
- System packages.
- Particular configurations.

Without a consistent environment, an application might work on one machine but fail on another.

Docker helps package the application and its dependencies into an image.

**Example:**

A Python application requires Python 3.12 and several packages. Instead of manually installing everything on every machine, you can define the environment in a Dockerfile and build an image from it.

### Containers vs Virtual Machines

| Containers                       | Virtual Machines             |
| -------------------------------- | ---------------------------- |
| Share the host OS kernel         | Run a guest operating system |
| Usually lightweight              | Usually heavier              |
| Start quickly                    | Generally start more slowly  |
| Isolated processes and resources | Virtualized hardware and OS  |

Containers are not completely equivalent to virtual machines. They provide process and resource isolation while sharing the host kernel.

On macOS and Windows, Docker Desktop uses a Linux virtual machine to run Linux containers.

---

## 3. Core Docker concepts

### 3.1 Docker Engine

The Docker Engine is the technology responsible for building and running containers.

Main components:

- **Docker daemon (`dockerd`):** Background service that manages Docker objects.
- **Docker CLI:** Command-line interface used to interact with Docker.
- **Docker API:** Interface through which clients communicate with the daemon.

Typical architecture:

```text
User
  |
  v
Docker CLI
  |
  v
Docker API
  |
  v
Docker daemon
  |
  +-- Images
  +-- Containers
  +-- Networks
  +-- Volumes
```

### 3.2 Images

An image is a template used to create containers.

Images can contain:

- Base operating system files.
- Runtime environments.
- Application code.
- Dependencies.
- Configuration and startup instructions.

Images are usually built in layers. Docker can reuse unchanged layers to make subsequent builds more efficient.

Examples of image names:

```text
nginx:latest
python:3.12
postgres:17
ubuntu:24.04
```

The part after `:` is the tag.

For reproducible environments, prefer specific versions or immutable image digests rather than relying on `latest`.

### 3.3 Containers

A container is an instance of an image.

Important characteristics:

- Containers are isolated from other containers by default.
- A container can be running, stopped, or removed.
- Container filesystems are generally ephemeral.
- Data that must survive container replacement should be stored in volumes or other persistent storage.

Example:

```bash
docker run nginx
```

This creates and starts a container from the Nginx image.

### 3.4 Image vs Container

A useful analogy:

- Image = Class or template.
- Container = Running instance.

One image can be used to create multiple containers.

---

## 4. Essential Docker commands

### 4.1 Check Docker installation

```bash
docker --version
docker version
docker info
```

- `docker --version`: Shows the CLI version.
- `docker version`: Shows client and server versions.
- `docker info`: Displays information about the Docker environment.

### 4.2 Images

```bash
docker images
docker pull nginx
docker pull python:3.12
docker image inspect nginx
docker rmi nginx
```

| Command                | Purpose               |
| ---------------------- | --------------------- |
| `docker images`        | List local images     |
| `docker pull`          | Download an image     |
| `docker image inspect` | Display image details |
| `docker rmi`           | Remove an image       |

An image cannot normally be removed while a container still references it.

### 4.3 Containers

```bash
docker ps
docker ps -a
docker run nginx
docker run -d nginx
docker stop CONTAINER_ID
docker start CONTAINER_ID
docker restart CONTAINER_ID
docker rm CONTAINER_ID
docker logs CONTAINER_ID
docker exec -it CONTAINER_ID sh
```

| Command          | Purpose                                      |
| ---------------- | -------------------------------------------- |
| `docker ps`      | List running containers                      |
| `docker ps -a`   | List all containers                          |
| `docker run`     | Create and start a container                 |
| `docker stop`    | Stop a running container                     |
| `docker start`   | Start an existing stopped container          |
| `docker restart` | Restart a container                          |
| `docker rm`      | Remove a container                           |
| `docker logs`    | Display container logs                       |
| `docker exec`    | Execute a command inside a running container |

`CONTAINER_ID` can usually be replaced with the container name.

### 4.4 Useful `docker run` options

```bash
docker run -d nginx
docker run --name web nginx
docker run -p 8080:80 nginx
docker run -d --name web -p 8080:80 nginx
docker run -it ubuntu:24.04 bash
docker run --rm ubuntu:24.04 echo "Hello Docker"
```

| Option   | Meaning                                           |
| -------- | ------------------------------------------------- |
| `-d`     | Run in detached mode (background)                 |
| `-it`    | Interactive terminal                              |
| `--name` | Assign a container name                           |
| `-p`     | Publish a container port                          |
| `-v`     | Mount a volume or host directory                  |
| `--rm`   | Automatically remove the container after it exits |
| `-e`     | Set an environment variable                       |

### 4.5 Port mapping

```bash
docker run -d -p 8080:80 nginx
```

The format is:

```text
HOST_PORT:CONTAINER_PORT
```

In this example:

- Host port: `8080`
- Container port: `80`

You can access the Nginx web server through:

```text
http://localhost:8080
```

Port mapping makes a container service accessible through the host. It does not mean every container port is automatically exposed.

---

## 5. Dockerfile

A Dockerfile contains instructions used to build an image.

Example:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

### Common Dockerfile instructions

| Instruction  | Purpose                                                 |
| ------------ | ------------------------------------------------------- |
| `FROM`       | Select a base image                                     |
| `WORKDIR`    | Set the working directory                               |
| `COPY`       | Copy files into the image                               |
| `ADD`        | Copy files with additional features                     |
| `RUN`        | Execute commands during image build                     |
| `CMD`        | Default command when a container starts                 |
| `ENTRYPOINT` | Configure the main executable                           |
| `EXPOSE`     | Document the port the application listens on            |
| `ENV`        | Set environment variables                               |
| `ARG`        | Define build-time variables                             |
| `USER`       | Set the user used for subsequent operations and runtime |

### CMD vs RUN vs ENTRYPOINT

These instructions have different purposes:

- `RUN`: Executes during image building.
- `CMD`: Defines the default command or arguments when a container starts.
- `ENTRYPOINT`: Defines the main executable for the container.

Example:

```dockerfile
FROM ubuntu:24.04

RUN apt-get update && apt-get install -y curl

CMD ["echo", "Container started"]
```

The `RUN` instruction executes while building the image. The `CMD` instruction executes when the container starts.

### Build an image

```bash
docker build -t my-app:1.0 .
```

- `build`: Build an image.
- `-t`: Assign a name and tag.
- `.`: Use the current directory as the build context.

Run it:

```bash
docker run my-app:1.0
```

---

## 6. Docker volumes and persistent storage

By default, data written inside a container's writable filesystem can disappear when the container is removed.

Volumes provide persistent storage independent of a container's lifecycle.

### 6.1 Named volumes

Create a volume:

```bash
docker volume create my-data
```

Use it:

```bash
docker run -d \
  --name database \
  -v my-data:/var/lib/postgresql/data \
  postgres:17
```

List volumes:

```bash
docker volume ls
```

Inspect a volume:

```bash
docker volume inspect my-data
```

Remove a volume:

```bash
docker volume rm my-data
```

Do not remove a volume containing important data unless you have a backup.

### 6.2 Bind mounts

Bind mounts connect a specific host directory or file to a container.

Example:

```bash
docker run -d \
  -p 8080:80 \
  -v ./website:/usr/share/nginx/html \
  nginx
```

Changes made to the host directory can be reflected inside the container.

### Volume vs Bind Mount

| Volume                               | Bind mount                               |
| ------------------------------------ | ---------------------------------------- |
| Managed by Docker                    | Uses a specific host path                |
| Good for persistent application data | Useful for development and sharing files |
| Docker controls storage location     | User specifies the host location         |

---

## 7. Docker networking

Docker networks allow containers to communicate.

Common network drivers:

| Network   | Purpose                                                             |
| --------- | ------------------------------------------------------------------- |
| `bridge`  | Default network type for standalone containers                      |
| `host`    | Uses the host network directly (platform-dependent behavior)        |
| `none`    | Disables container networking                                       |
| `overlay` | Connects containers across Docker hosts in supported configurations |

### List networks

```bash
docker network ls
```

### Create a network

```bash
docker network create app-network
```

### Connect a container

```bash
docker network connect app-network CONTAINER_ID
```

### Inspect a network

```bash
docker network inspect app-network
```

Containers on the same user-defined bridge network can communicate using container names as DNS hostnames.

For example, an application container might connect to a database using:

```text
postgres://user:password@database:5432/mydb
```

Here, `database` is the database container's network name or alias.

---

## 8. Docker Compose

Docker Compose is used to define and manage multi-container applications.

Instead of starting each container manually, you define services in a YAML file, usually named `compose.yaml`.

Example:

```yaml
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"

  database:
    image: postgres:17
    environment:
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: change-this-password
      POSTGRES_DB: appdb
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
```

**Important:** This example is for learning. Do not use hardcoded database passwords in production. Use an appropriate secrets-management approach.

### Common Compose commands

```bash
docker compose up
docker compose up -d
docker compose down
docker compose ps
docker compose logs
docker compose logs -f
docker compose build
docker compose pull
```

| Command                  | Purpose                                                    |
| ------------------------ | ---------------------------------------------------------- |
| `docker compose up`      | Create and start services                                  |
| `docker compose up -d`   | Start services in the background                           |
| `docker compose down`    | Stop and remove containers and networks created by Compose |
| `docker compose ps`      | List services and their status                             |
| `docker compose logs`    | Display service logs                                       |
| `docker compose logs -f` | Follow logs continuously                                   |
| `docker compose build`   | Build or rebuild service images                            |
| `docker compose pull`    | Download service images                                    |

By default, `docker compose down` does not remove named volumes. Use `docker compose down -v` only when you intentionally want to delete them.

---

## 9. Environment variables

Environment variables allow configuration to be passed to containers.

Example:

```bash
docker run -e APP_ENV=development my-app
```

In a Dockerfile:

```dockerfile
ENV APP_ENV=production
```

In Compose:

```yaml
services:
  app:
    image: my-app
    environment:
      APP_ENV: development
```

Do not store passwords, API keys, or tokens in Dockerfiles, public repositories, or ordinary environment configuration files committed to Git.

---

## 10. Docker security fundamentals

Docker improves isolation and deployment consistency, but containers are not automatically secure.

Important practices:

### Use trusted images

- Prefer official images or trusted publishers.
- Use specific image versions.
- Keep base images updated.
- Scan images for known vulnerabilities.

### Avoid running as root

Where possible, create and use a non-root user inside the container.

Example:

```dockerfile
RUN useradd --create-home appuser

USER appuser
```

The exact setup depends on the base image and application.

### Minimize the image

- Use small, appropriate base images.
- Install only required packages.
- Use multi-stage builds when useful.
- Avoid including unnecessary development tools.

### Protect secrets

- Never commit secrets to Git.
- Avoid embedding credentials in images.
- Use dedicated secret-management solutions.
- Restrict access to Docker daemon and socket.

### Understand Docker socket access

Access to the Docker daemon can provide extensive control over the host system. Treat access to `/var/run/docker.sock` as highly privileged.

### Additional topics to learn

- Image vulnerability scanning.
- Container runtime security.
- Linux capabilities.
- Seccomp profiles.
- AppArmor and SELinux.
- Read-only filesystems.
- Resource limits.
- Supply-chain security and image signing.

These topics are particularly relevant to cloud security and DevSecOps.

---

## 11. Troubleshooting

### Docker daemon is not running

Possible symptom:

```text
Cannot connect to the Docker daemon
```

Possible solution:

- Start Docker Desktop on macOS or Windows.
- Check whether Docker Engine is running.
- Run `docker info` again.

### Port is already allocated

Possible symptom:

```text
port is already allocated
```

Another process or container is already using that host port.

Check:

```bash
docker ps
```

Use a different host port if necessary.

### Container exits immediately

Check:

```bash
docker ps -a
docker logs CONTAINER_ID
```

A container stops when its main process exits. Inspect logs and the configured startup command.

### Image not found

Possible causes:

- Incorrect image name.
- Incorrect tag.
- Image is not available locally.
- Registry authentication is required.

Try:

```bash
docker pull IMAGE_NAME:TAG
```

### Changes are not reflected

Possible causes:

- The image was not rebuilt.
- The container is using an old image.
- The host directory is not mounted correctly.
- The application needs to be restarted.

Check the container configuration and rebuild when appropriate.

---

## 12. Docker cleanup

Useful commands:

```bash
docker container prune
docker image prune
docker volume prune
docker system df
docker system prune
```

| Command                  | Purpose                   |
| ------------------------ | ------------------------- |
| `docker container prune` | Remove stopped containers |
| `docker image prune`     | Remove unused images      |
| `docker volume prune`    | Remove unused volumes     |
| `docker system df`       | Show Docker disk usage    |
| `docker system prune`    | Remove unused Docker data |

**Warning:** Cleanup commands can remove resources you intended to keep. Inspect what will be affected before confirming.

Avoid using `docker system prune -a --volumes` casually.

---

## 13. Practical learning exercises

Complete these exercises to verify your understanding.

- [ ] Check Docker CLI and Engine versions.
- [ ] Download the Nginx image.
- [ ] Run an Nginx container in detached mode.
- [ ] Map host port `8080` to container port `80`.
- [ ] Open `http://localhost:8080`.
- [ ] Inspect the container with `docker ps`.
- [ ] Read its logs with `docker logs`.
- [ ] Stop and remove the container.
- [ ] Create a simple Dockerfile for a Python application.
- [ ] Build and run your own image.
- [ ] Create a named volume and mount it.
- [ ] Create a custom Docker network.
- [ ] Use Docker Compose to run an application and a database.
- [ ] Inspect the images and containers you created.

---

## 14. Important distinctions to remember

| Question                                           | Answer                                                        |
| -------------------------------------------------- | ------------------------------------------------------------- |
| What is an image?                                  | A template used to create containers                          |
| What is a container?                               | An isolated instance of an image                              |
| What is a Dockerfile?                              | Instructions for building an image                            |
| What is a volume?                                  | Persistent storage managed by Docker                          |
| What is a bind mount?                              | A mount connecting a host path to a container                 |
| What is a Docker network?                          | A way for containers to communicate                           |
| What is Docker Compose?                            | A tool for defining and managing multi-container applications |
| Does `EXPOSE` publish a port?                      | No. It documents a container port; `-p` publishes it          |
| Does removing a container remove its named volume? | Normally, no                                                  |
| Does stopping a container remove it?               | No                                                            |

---

## 15. Official resources

- [Docker Documentation](https://docs.docker.com/)
- [Docker Get Started](https://docs.docker.com/get-started/)
- [Docker Engine](https://docs.docker.com/engine/)
- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Docker Compose](https://docs.docker.com/compose/)
- [Docker networking](https://docs.docker.com/engine/network/)
- [Docker storage](https://docs.docker.com/engine/storage/)
- [Docker security](https://docs.docker.com/engine/security/)

---

## 16. Personal notes and questions

Use this section to record things you have tested, misunderstood, or want to investigate.

### Things I understand

-

### Things I need to practice

-

### Questions

- What exactly happens when Docker builds an image layer?
- How does Docker networking work internally?
- What is the difference between Docker Engine and Docker Desktop?
- How does Docker integrate with Kubernetes?
- How should secrets be managed in production?
- How do containers work in AWS (ECS, EKS, and Fargate)?

### Related topics

- [ ] Linux namespaces and cgroups
- [ ] Container security
- [ ] Multi-stage Docker builds
- [ ] Docker Compose advanced configuration
- [ ] Kubernetes fundamentals
- [ ] AWS ECS and ECR
- [ ] CI/CD with Docker
- [ ] Terraform and container infrastructure
