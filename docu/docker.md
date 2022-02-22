## Docker

> Docker is container based technology and containers are just user space of the operating system.
> At the low level, a container is just a set of processes that are isolated from the rest of the 
> system, running from a distinct Image that provides all files necessary to support the processes.
> It is built for running applications. In Docker, the containers running share the host OS kernel.

Docker is a:
- tech-tool to **package** an application with all the necessary **dependencies and configuration**.
- portable artifact, easily shared and moved around.
- tool which make development and deployment process more efficiant.

### Difference between Docker and Virtual Machine

- Docker virtualize the application layer.

- The Virtual Machine virtualize the application layer and the kernel, the complete operation system.

```diff
+ Docker is lighter and faster but it needs docker toolbox to run out of Linux Kernel. @@
```

### Docker Elements

The two basic elements in Docker are:

- Image
- Container

#### Images

An Image is an artifact, a package with all the necessary dependencies and configuration.

A docker Image is always made by layers of Images:

1. Application Image
2. Intermediate Images.
3. Linux Base Image (alpine:3.10)

The benefit of using layers of Images is that if a layer change, only one Image is downloaded.

#### Container

A container is the **running environment for an Image**.

### Container Process

After an Image is pull to a host machine (server or local machine), it creates a container environment, which run in a the machine.

Multiple container can run in a host machine.

The container process that runs is isolated in that it has its own file system, its own networking, and its own isolated process tree separate from the host.

Containers listen to a especific **port**, to talk to the application running inside of a container.

These port is bind with a local port or host port.

```diff
+ There can be issues if the port is already used locally.In that case, a new host port should be binded.
```

### Where are container Images stored?

Container Images live in a Container Image Repository, private Repositories and Public Repositories (DockerHub).

- One command to fetch the container and install it.

- It has its own isolated operation system (environment) with all the needed configuration, the dependencies and a start script.

- It can run the same app locally with 2 different versions without conflicts.

### Set up an containerized Application

- Developers and Operations work together to package the application's configuration and dependencies in a container.

- No environmental configuration needed on Server.

- Install **Docker Run Time Engine** on the server.

- Run a **docker command** that pull the container Image from the repository and run it.

Important files:

- Docker Compose

A docker compose maps commands into a file called docker-compose.yaml

- Dockerfile

The dockerfile is a Blueprint for docker Images

Start by basing it on another Image

| task | script |
| ---- | ---- |
| install | **FROM** node |
| set MONGO_DB_USERNAME=admin set MONDO_DB_PWD=password | **ENV** MONGO_DB_USERNAME=admin \ MONDO_DB_PWD=password |
| create/home/app folder | **RUN** mkdir -p /home/app (execute linux command) this directory li created inside the container |
| copy current folder filer to /home/app | **COPY** ./home/app (execute on the host) |
| start the app with: "node server.js" | CMD["node", "server.js"] entry pint command - run inside the container |

### Docker volumes

Docker volumes are used for **data persistence**.

In a docker volume the **host file system** is pluged with a **virtual file system**.
Folder in physical host file system is mounted into the virtual file system of Docker.

##### Host Volumes

Defined where on the host file system the reference between the host and the container is made.

```sh
docker run -v <host-directory>:/<container-directory>
```

##### Anonimous Volumes

Reference only the container. For each container a folder is generated atomatically by docker that gets mounted

```sh
docker run -v <container-directory>
```

##### Named Volumes

The recommended Volume. You can reference the volume by name.

```sh
docker run -v name:<host-directory>
```

##### Define Volumes in docker-compose.yaml

```yaml
services:
  mongodb:
    image: mongo
    volumes:
        - mongo-data:/data/db
volumes:
  mongo-data:
    driver: local
```

- mongo-data:/data/db: the path where the data is store in the container. Check it:

```sh 
docker exec -it <container-name> 
```
- volumes: defines your volumes, the name and the driver - physical store in the local file system.

#### Where is the docker volume location

| OS | volume location |
| -- | --- |
| windows | C:\ProgramData\docker\volumes |
| linux | /var/lib/docker/volumes |
| mac | /var/lib/docker/volumes |