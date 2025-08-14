# Docker Commands

## Recap

Some of the most important and used docker commands and their uses.

While working with docker the terms **container** and **image** will be used a lot.

**What is a Docker Image?**

A read-only blueprint containing every detail required to run an application like configuration, dependencies etc.
This is static meaning it doesn't change once built. Docker image is used to create containers. Docker images
are stored in docker registries, these registries can be public or private to a organization.

**What is a Docker Container?**

A container is an isolated environment where an application can be executed. Containers are lightweight
and contains the dependencies needed by the application for it's execution. Container is a instance of a
Docker Image.

## Basic Docker Commands

### Image Management

> Pull an image

 ```bash
    # Pull a image from the repository
    # If you don't use the tag the default latest tag will be used
    docker pull <image-name:tag>
 ```

> List all images

```bash
    # List all docker images
    docker images
```

> Remove an image

```bash
    # First list the images and note down the image id
    # Verify the docker image has been removed
    docker rmi <image_id>
```

> Build a custom docker image

```bash
    # Example a java spring rest api
    # You need to have a working Dockerfile
    # . indicates the current working directory
    docker build -t <image-name:tag-name> .
```

### Container Management

> List running containers

```bash
    docker ps
```

> List all containers including stopped containers

```bash
    docker ps -a
```

> Run a docker container

```bash
    # There are more attributes that will be needed by the run command
    # Exmaple
    # docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:latest
    docker run <image-name>
```

> Stop a container

```bash
    docker stop <container_id>
```

> Start a container

```bash
    docker start <container_id>
```

> Restart a container

```bash
    docker restart <container_id>
```

> Remove a container

```bash
    # You cannot remove a running container unless you force it
    docker rm <container_id>
    # Forecefully remove a container
    docker rm <container_id> -f
```

> Running an interactive shell inside the container

```bash
    docker exec -it <container_id> /bin/bash
```

### Logs and Info

> Check logs

```bash
    docker logs <container_id>
```

> Inspect a container

```bash
    docker inspect <container_id>
```

> Docker stats

```bash
    docker stats
```

### Docker cleanup

> Remove unsued docker objects

```bash
    # Docker objects can refer to images, networks, volumes etc
    docker system prune
```

> Remove unused volumes

```bash
    docker volume prune
```

> Remove unused images

```bash
    docker image prune
```

