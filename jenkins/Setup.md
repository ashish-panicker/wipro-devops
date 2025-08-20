# Setup Jenkins on Windows OS

> Step 1: Create the docker network
```bash
docker network create jenkins
```

> Run a docker:dind (docker in docker) Docker image
```bash
    docker run --name jenkins-docker --rm --detach ^
    --privileged --network jenkins --network-alias docker ^
    --env DOCKER_TLS_CERTDIR=/certs ^
    --volume jenkins-docker-certs:/certs/client ^
    --volume jenkins-data:/var/jenkins_home ^
    --publish 2376:2376 ^
    docker:dind
```

> Customize the official Jenkins Docker image, by executing the following two steps:
```Dockerfile
FROM jenkins/jenkins:2.516.1-jdk21
USER root
RUN apt-get update && apt-get install -y lsb-release
RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
  https://download.docker.com/linux/debian/gpg
RUN echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
  https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
  
RUN apt-get update && apt-get install -y docker-ce-cli
USER jenkins
RUN jenkins-plugin-cli --plugins "blueocean docker-workflow json-path-api"
```

> Build a new docker image from this Dockerfile and assign the image a meaningful name, e.g. "myjenkins-blueocean:2.516.1-1":
```bash
  docker build -t myjenkins-blueocean:2.516.1-1 .
```

> Run your own myjenkins-blueocean:2.516.1-1 image as a container in Docker using the following docker run command:
```bash
    docker run --name jenkins-blueocean --restart=on-failure --detach ^
    --network jenkins --env DOCKER_HOST=tcp://docker:2376 ^
    --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 ^
    --volume jenkins-data:/var/jenkins_home ^
    --volume jenkins-docker-certs:/certs/client:ro ^
    --publish 8080:8080 --publish 50000:50000 myjenkins-blueocean:2.516.1-1
```