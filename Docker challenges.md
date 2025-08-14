# Docker Challenges

Here are some problem statements to practice docker commands.

## **Beginner Level**

1. **Pull and List Images**
   Pull the official `nginx` image (latest version) from Docker Hub and verify it has been downloaded by listing all available images.

2. **Run a Basic Container**
   Run an `nginx` container named `webserver` in detached mode and map its port `80` to host port `9090`. Verify that you can access it in a browser.

3. **Stop, Start, and Remove a Container**
   Start an `alpine` container interactively, run `ls /`, then exit. Stop, start, and finally remove the container.

4. **Build and Run a Custom Image**
   Given a `Dockerfile` containing a simple Node.js app, build an image named `nodeapp:1.0` and run it so it’s accessible at `http://localhost:5000`.

---

## **Intermediate Level**

5. **Run MySQL in a Container**
   Create and run a MySQL container named `mysql-db` with:

   * root password `rootpass`
   * a database named `company`
   * a user `devuser` with password `devpass`
     Map MySQL’s port to host port `3307`. Verify it’s running.

6. **Container Logs and Inspection**
   Run a container from the `busybox` image that prints `Hello Docker!` every 5 seconds. View the logs and inspect the container to see its network configuration.

7. **Docker Cleanup Task**
   Remove all stopped containers, unused images, and unused volumes from your system using the appropriate Docker commands.

8. **Custom Network with Multiple Containers**
   Create a Docker network `app_net`.

   * Run a MySQL container named `mysql-app` in `app_net`.
   * Run a phpMyAdmin container in the same network to manage MySQL via browser.
     Access phpMyAdmin at `http://localhost:8081`.

---

## **Advanced Level**

9. **Docker Multi-Container Application**
   Use Docker to run:

   * A backend API (Spring Boot or Node.js) on `app_net`
   * A MySQL database container on `app_net`
   * A frontend container (React, Angular, or Vue) that communicates with the backend by container name.
     Ensure all services run via the same custom bridge network and can be accessed externally via mapped ports.

10. **Persistent MySQL Data**
    Modify your MySQL container setup so that data is stored in a named volume (`mysql_data`) and remains available even if the container is removed. Demonstrate by inserting data, removing the container, and starting a new one with the same volume.

11. **Simulating Host Networking**
    Run an `nginx` container using the `host` network mode. Verify that it binds directly to the host’s IP without port mapping. Discuss when using `host` mode might be beneficial or risky.

12. **Docker System Resource Monitoring**
    Start three containers (`nginx`, `mysql`, and `redis`), then use `docker stats` to monitor their CPU and memory usage. Kill the one consuming the most memory.

---