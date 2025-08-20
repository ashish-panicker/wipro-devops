FROM jenkins/jenkins:lts-jdk21

USER root

# Install necessary packages
RUN apt-get update && apt-get install -y \
  lsb-release \
  curl \
  gnupg \
  ca-certificates \
  apt-transport-https \
  software-properties-common 

# Install Docker CLI
RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc https://download.docker.com/linux/debian/gpg \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.asc] https://download.docker.com/linux/debian $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list \
  && apt-get update && apt-get install -y docker-ce-cli

# install azure cli
RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash

# installs kubectl
RUN KUBECTL_VERSION=$(curl -sL https://dl.k8s.io/release/stable.txt) \
    && curl -LO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl"
RUN install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
RUN rm kubectl
# Clean up
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

USER jenkins

# Install Jenkins plugins
RUN jenkins-plugin-cli --plugins "blueocean docker-workflow json-path-api kubernetes-cli"
RUN jenkins-plugin-cli --plugins "blueocean docker-workflow json-path-api"