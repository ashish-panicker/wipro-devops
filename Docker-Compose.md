# Docker compose

Docker Compose is a tool that lets you define and run multi-container Docker applications using a single file (docker-compose.yml).

Instead of running long docker run ... commands multiple times, you:
1. Describe your containers (services, networks, volumes, environment variables) in YAML.
2. Start everything with one command:
   ```bash
    docker compose up -d
    ```
## Why use docker compose?

**Purpose**
- Manage multi-container apps easily
- Store configuration in one place
- Make setup reproducible & shareable
  

| Advantage           | Without Compose                | With Compose                    |
| ------------------- | ------------------------------ | ------------------------------- |
| **Startup**         | Multiple `docker run` commands | Single `docker compose up`      |
| **Reproducibility** | Manual typing → error-prone    | One config file, consistent     |
| **Networking**      | Must manually create & connect | Auto-creates & connects         |
| **Portability**     | Commands not easily shared     | Just share `docker-compose.yml` |
| **Persistence**     | Need manual `-v` mapping       | Defined once in YAML            |


### Demo 1

Start a `Nginx` container

**Command Line**

```bash
docker run -d --name web -p 8080:80 nginx:latest
```

**Docker compose**
```yaml
# docker-compose.yaml
version: "3.9"
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
```

### Demo 2

Start `Nginx + Redis` containers

**Command Line**

```bash
# Create docker network
docker network create demo_net

# Run Nginx
docker run -d ^
  --name web ^
  --network demo_net ^
  -p 8080:80 ^
  nginx:latest

# Run Redis
docker run -d ^
  --name cache ^
  --network demo_net ^
  -p 6379:6379 ^
  redis:latest
```

**Docker compose**

In this demo:
- Both containers run together.
- Docker Compose automatically creates a default network so they can talk to each other by name

```yaml
# docker-compose.yaml
version: "3.9"
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"

  cache:
    image: redis:latest
    ports:
      - "6379:6379"

```

### Demo 3

`Mysql + phpMyAdmin` with volumes and networks


```yaml
version: "3.9"

services:
  mysql:
    image: mysql:8.0
    container_name: mysql-server
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: secret123
      MYSQL_DATABASE: mydb
      MYSQL_USER: myuser
      MYSQL_PASSWORD: mypassword
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - mysql_net

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: myadmin
    restart: always
    environment:
      PMA_HOST: mysql
    ports:
      - "8080:80"
    networks:
      - mysql_net

networks:
  mysql_net:

volumes:
  mysql_data:
```

### Demo 4

`Mysql + phpMyAdmin` with volumes and networks and `.env` files.