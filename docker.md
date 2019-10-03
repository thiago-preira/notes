# Docker Summary

## 1 - Images

Like virtual machine templates and are used to start containers. Under the hood they are made up one or more read-only layers, that when stacked together, make up the overall image.

### 1.1 - Commands

* `docker image pull` is the command to download images. We pull images from repositories inside of remote registries. By default, images will be pulled
from repositories on Docker Hub. This command will pull the image tagged as latest from the alpine repository on Docker Hub `docker image pull alpine:latest`.

* `docker image ls` lists all of the images stored in your Docker host’s local cache. To see the SHA256 digests of images add the `--digests` flag.

* `docker image inspect` is a thing of beauty! It gives you all of the glorious details of an image — layer data and metadata.

* `docker image rm` is the command to delete images. This command shows how to delete the alpine:latest image — `docker image rm alpine:latest`. You
cannot delete an image that is associated with a container in the running (Up) or stopped (Exited) states.

## 2 - Containers

A container is the runtime instance of an image.

### 2.1 - Commands

* `docker container run` is the command used to start new containers. In its simplest form, it accepts an image and a command as arguments. The image is used to create the container and the command is the application you want the container to run. This example will start an Ubuntu container in the foreground, and tell it to run the Bash shell: `docker container run -it ubuntu /bin/bash`.

* `Ctrl-PQ` will detach your shell from the terminal of a container and leave the container running (UP) in the background.

* `docker container ls` lists all containers in the running (UP) state. If you add the `-a` flag you will also see containers in the stopped (Exited) state.

* `docker container exec` lets you run a new process inside of a running container. It’s useful for attaching the shell of your Docker host to a terminal inside of a running container. This command will start a new Bash shell inside of a running container and connect to it: `docker container exec -it <container-name or container-id> bash`. For this to work, the image used to create your container must contain the Bash shell.

* `docker container stop` will stop a running container and put it in the Exited (0) state. It does this by issuing a SIGTERM to the process with PID 1 inside of the container. If the process has not cleaned up and stopped within 10 seconds, a SIGKILL will be issued to forcibly stop the container. `docker container stop` accepts container IDs and container names as arguments.

* `docker container start` will restart a stopped (Exited) container. You can give docker container start the name or ID of a container.

* `docker container rm` will delete a stopped container. You can specify containers by name or ID. It is recommended that you stop a container with the `docker container stop` command before deleting it with docker container rm.

* `docker container inspect` will show you detailed configuration and runtime information about a container. It accepts container names and container IDs as its main argument.

## 3 - Containerizing an app (Dockerfile)

Dockerfile containing instructions on how to build the application into an image.

### 3.1 - Commands

* `docker image build` is the command that reads a Dockerfile and containerizes an application. The `-t` flag tags the image, and the `-f` flag lets you specify the name and location of the Dockerfile. With the `-f` flag, it is possible to use a Dockerfile with an arbitrary name and in an arbitrary location. The build context is where your application files exist, and this can be a directory on your local Docker host or a remote Git repo.

* The `FROM` instruction in a Dockerfile specifies the base image for the new image you will build. It is usually the first instruction in a Dockerfile.

* The `RUN` instruction in a Dockerfile allows you to run commands inside the image which create new layers. Each `RUN` instruction creates a single new layer.

* The `COPY` instruction in a Dockerfile adds files into the image as a new layer. It is common to use the `COPY` instruction to copy your application code into an image.

* The `EXPOSE` instruction in a Dockerfile documents the network port that the application uses.

* The `ENTRYPOINT` instruction in a Dockerfile sets the default application to run
when the image is started as a container.

* Other Dockerfile instructions include `LABEL`, `ENV`, `ONBUILD`, `HEALTHCHECK`, `CMD`

## 4 - Docker Compose

Deploys and manages multi-container applications on Docker nodes operating in **single-engine mode**

### 4.1 - Commands

* `docker-compose` up is the command we use to deploy a Compose app. It expects the Compose file to be called docker-compose.yml or docker-compose.yaml, but you can specify a custom filename with the `-f` flag. It’s common to start
the app in the background with the `-d` flag.

* `docker-compose stop` will stop all of the containers in a Compose app without deleting them from the system. The app can be easily restarted with dockercompose restart.

* `docker-compose rm` will delete a stopped Compose app. It will delete containers and networks, but it will not delete volumes and images.

* `docker-compose restart` will restart a Compose app that has been stopped with docker-compose stop. If you have made changes to your Compose app
since stopping it, these changes will not appear in the restarted app. You will need to re-deploy the app to get the changes.

* `docker-compose ps` will list each container in the Compose app. It shows current state, the command each one is running, and network ports.

* `docker-compose down` will stop and delete a running Compose app. It deletes containers and networks, but not volumes and images.

### 4.2 - Docker Compose Example

```YML
version: "3.5"
services:
  web-fe:
    build: .
    command: python app.py
    ports:
      - target: 5000
      published: 5000
    networks:
      - counter-net
    volumes:
      - type: volume
      source: counter-vol
      target: /code
  redis:
    image: "redis:alpine"
    networks:
      counter-net:

networks:
  counter-net:
volumes:
  counter-vol:
```

## 5 -  Docker Swarm

Docker Swarm is two things: an enterprise-grade secure cluster of Docker hosts, and an engine for orchestrating microservices apps.

### 5.1 - Commands

* `docker swarm init` is the command to create a new swarm. The node that you run the command on becomes the first manager and is switched to run in swarm mode.

* `docker swarm join-token` reveals the commands and tokens needed to join workers and managers to existing swarms. To expose the command to join a new manager, use the `docker swarm join-token manager` command. To get the command to join a worker, use the `docker swarm join-token worker`
command.
* `docker node ls` lists all nodes in the swarm including which are managers and which is the leader.

* `docker service create` is the command to create a new service.

* `docker service ls` lists running services in the swarm and gives basic info on the state of the service and any replicas it’s running.

* `docker service ps <service>` gives more detailed information about individual service replicas.

* `docker service inspect` gives very detailed information on a service. It accepts the `--pretty` flag to limit the information returned to the most important information.

* `docker service scale` lets you scale the number of replicas in a service up and down.

* `docker service update` lets you update many of the properties of a running service.

* `docker service logs` lets you view the logs of a service.

* `docker service rm` is the command to delete a service from the swarm. Use it with caution as it deletes all service replicas without asking for confirmation.

## 6 - Docker Networking

Docker networking has its own docker network sub-command.

### 6.1 - Commands

* `docker network ls` Lists all networks on the local Docker host.

* `docker network create` Creates new Docker networks. By default, it creates them with the nat driver on Windows, and the bridge driver on Linux. You can specify the driver (type of network) with the `-d` flag. `docker network create
-d overlay overnet` will create a new overlay network called overnet with the native Docker overlay driver.

* `docker network inspect` Provides detailed configuration information about a Docker network.

* `docker network prune` Deletes all unused networks on a Docker host.

* `docker network rm` Deletes specific networks on a Docker host.

## 7 - Volumes

Docker volumes are first-class citizens in the Docker API, and are managed indepen-
dently of containers with their own docker volume sub-command. This means that
deleting a container will not delete the volumes it was using.
Volumes are the recommended way to work with persistent data in a Docker
environment.

### 7.1 - Commands

* `docker volume create` is the command we use to create new volumes. By default, volumes are created with the native local driver, but you can use the `-d` flag to specify a different driver.

* `docker volume ls` will list all volumes on the local Docker host.

* `docker volume inspect` shows detailed volume information. Use this command to find out where a volume exists in the Docker host’s filesystem.

* `docker volume prune` will delete all volumes that are not in use by a container or service replica. Use with caution!

* `docker volume rm` deletes specific volumes that are not in use.

## 8 - Docker Stack

Stacks are the native Docker solution for deploying and managing multi-service applications.

### 8.1 - Commands

* `docker stack deploy` is the command we use to deploy and update stacks of services defined in a stack file (usually `docker-stack.yml` ).

* `docker stack ls` will list all stacks on the Swarm, including how many services they have.

* `docker stack ps` gives detailed information about a deployed stack. It accepts the name of the stack as its main argument, lists which node each replica is
running on, and shows desired state and current state.

* `docker stack rm` is the command to delete a stack from the Swarm. It does not ask for confirmation before deleting the stack.

### 8.2 - Stack Example

```YML
version: "3.2"

services:
  reverse_proxy:
    image: dockersamples/atseasampleshopapp_reverse_proxy
    ports:
      - "80:80"
      - "443:443"
    secrets:
      - source: revprox_cert
        target: revprox_cert
      - source: revprox_key
        target: revprox_key
    networks:
      - front-tier

  database:
    image: dockersamples/atsea_db
    environment:
      POSTGRES_USER: gordonuser
      POSTGRES_DB_PASSWORD_FILE: /run/secrets/postgres_password
      POSTGRES_DB: atsea
    networks:
      - back-tier
    secrets:
      - postgres_password
    deploy:
      placement:
        constraints:
          - 'node.role == worker'

  appserver:
    image: dockersamples/atsea_app
    networks:
      - front-tier
      - back-tier
      - payment
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
        failure_action: rollback
      placement:
        constraints:
          - 'node.role == worker'
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s
    secrets:
      - postgres_password

  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "8001:8080"
    stop_grace_period: 1m30s
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      update_config:
        failure_action: rollback
      placement:
        constraints:
          - 'node.role == manager'

  payment_gateway:
    image: dockersamples/atseasampleshopapp_payment_gateway
    secrets:
      - source: staging_token
        target: payment_token
    networks:
      - payment
    deploy:
      update_config:
        failure_action: rollback
      placement:
        constraints:
          - 'node.role == worker'
          - 'node.labels.pcidss == yes'

networks:
  front-tier:
  back-tier:
  payment:
    driver: overlay
    driver_opts:
      encrypted: 'yes'

secrets:
  postgres_password:
    external: true
  staging_token:
    external: true
  revprox_key:
    external: true
  revprox_cert:
    external: true
```
