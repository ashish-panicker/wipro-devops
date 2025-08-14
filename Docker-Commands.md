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

### Explaining the `docker run command`

We will be using the `mysql:latest` image.

```bash
    # in linux and mac
    docker run \
    --name mysql-server \
    -e MYSQL_ROOT_PASSWORD=secret123 \
    -e MYSQL_DATABASE=mydb \
    -e MYSQL_USER=myuser \
    -e MYSQL_PASSWORD=mypassword \
    -p 3121:3306 \
    -d mysql:latest

    # In windows command prompt
    docker run ^
    --name mysql-server ^
    -e MYSQL_ROOT_PASSWORD=secret123 ^
    -e MYSQL_DATABASE=mydb ^
    -e MYSQL_USER=myuser ^
    -e MYSQL_PASSWORD=mypassword ^
    -p 3121:3306 ^
    -d mysql:latest

    # In powershell
    docker run `
    --name mysql-server `
    -e MYSQL_ROOT_PASSWORD=secret123 `
    -e MYSQL_DATABASE=mydb `
    -e MYSQL_USER=myuser `
    -e MYSQL_PASSWORD=mypassword `
    -p 3121:3306 `
    -d mysql:latest
```

| Flag / Attribute                   | Purpose                                                                                           |
| ---------------------------------- | ------------------------------------------------------------------------------------------------- |
| `docker run`                       | Start a new container from an image.                                                              |
| `--name mysql-server`              | Gives the container a custom name (instead of random one).                                        |
| `-e MYSQL_ROOT_PASSWORD=secret123` | Sets the root password (mandatory for MySQL unless using volume with pre-configured credentials). |
| `-e MYSQL_DATABASE=mydb`           | Creates a new database automatically on first run.                                                |
| `-e MYSQL_USER=myuser`             | Creates an additional user (optional).                                                            |
| `-e MYSQL_PASSWORD=mypassword`     | Sets the password for the above user.                                                             |
| `-p 3121:3306`                     | Maps container's MySQL port (3306) to host’s port 3121.                                           |
| `-d`                               | Run in detached (background) mode.                                                                |
| `mysql:8.0`                        | The image name and tag (version). `mysql` defaults to latest if no tag given.                     |

## Docker Network

### What is it?

A docker network is layer that defines how a docker container communicates:
- With each other
- With the host machine
- With the internet

### Types of network

| Network Type           | Scope                                         | When to Use                                                                                 | Example                          |
| ---------------------- | --------------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------- |
| **bridge** *(default)* | Local to the Docker host                      | When containers need to talk to each other on the same host, isolated from external systems | Web app ↔ DB on the same machine |
| **host**               | Shares the host’s network stack               | When you want containers to use the host’s IP and ports directly                            | Performance-sensitive apps       |
| **none**               | No networking                                 | When you want total network isolation                                                       | Secure batch jobs                |
| **overlay**            | Across multiple Docker hosts (Swarm mode)     | For multi-host communication                                                                | Distributed microservices        |
| **macvlan**            | Gives containers their own MAC address on LAN | When containers should appear as physical devices on the network                            | Legacy systems needing real IPs  |

### Important commands

> Create a network
```bash
    docker network create <network_name>
```

> Inspect a network
```bash
    docker network inspect bridge
```

> List networks
```bash
    docker network ls
```

> Connect a existing container to the network
```bash
    docker network connect <my_network> <my_container>
```

> Run a container with a specific network
```bash
    # Assuming the network is already created
    # Use the --network switch
    docker run -d --name <my_container> --network <my_network> nginx
```

### Docker networking example with:

1. MySQL server container
2. phpMyAdmin (a web-based MySQL client)
3. A user-defined bridge network so they can talk to each other by name

**Step 1: Create a user defined bridge network**
```bash
    docker network create mysql_net
```

**Step 2: Run the mysql container**
```bash
    # Lokout for the --network switch
    docker run -d \
    --name mysql-server \
    --network mysql_net \
    -e MYSQL_ROOT_PASSWORD=secret123 \
    -e MYSQL_DATABASE=mydb \
    -e MYSQL_USER=myuser \
    -e MYSQL_PASSWORD=mypassword \
    mysql:latest

    # Windows
    docker run ^
    --name mysql-server ^
    --network mysql_net ^
    -e MYSQL_ROOT_PASSWORD=secret123 ^
    -e MYSQL_DATABASE=mydb ^
    -e MYSQL_USER=myuser ^
    -e MYSQL_PASSWORD=mypassword ^
    -d mysql:latest

```

**Step 3: Run the phpMyAdmin container**
```bash
    docker run -d \
    --name myadmin \
    --network mysql_net \
    -e PMA_HOST=mysql-server \
    -p 8080:80 \
    phpmyadmin/phpmyadmin

    # Windows
    docker run ^
    --name myadmin ^
    --network mysql_net ^
    -e PMA_HOST=mysql-server ^
    -p 8080:80 ^
    -d phpmyadmin/phpmyadmin

```
**Explanation:**

```
--name myadmin → Name of phpMyAdmin container.
--network mysql_net → Same network as MySQL, so they can communicate.
-e PMA_HOST=mysql-server → Hostname of MySQL server (container name).
-p 8080:80 → Maps port 8080 on host to port 80 in phpMyAdmin.
phpmyadmin/phpmyadmin → Official phpMyAdmin image
```

**Step 4: Access phpMyAdmin in Browser**
```
    URL: http://localhost:8080
    Server: mysql-server
    Username: myuser
    Password: mypassword
```