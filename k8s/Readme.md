# Kubernetes

## What is k8s?

It is a container orchestration platform. Docker helps us to create and build containers while k8s allows us to 
manage them. Kubenetes automates:
1. Deplyment of containers
2. Scaling of containers
3. Networking of containers
4. Load balancing
5. Rolling updates
6. Self Healing or Reselience

## How does it fit into the Devops ecosystem?

> Devops ecosystem
Code -> Build -> Test -> Deploy -> Monitor -> Feedback

Docker can be used in the `Build` and `Test` phases while k8s helps in the `Deploy` and `Manage` phases.

### Example workflow

1. Use Jenkins to build a pipeline which will in turn build a docker image of my spring boot app.
2. Docker image is pushed to a docker repository, like ECR, Dockerhub
3. K8s picks up the image and deploys it across a cluster
4. Monitoring tools like `promethues` and `grafana` can then help monitor the containers

## High level k8s architecture

1. Master Node(Control Plane)
   1. API Server (entry point)
   2. Scheduler (decides where to run a container)
   3. Controller Manager (ensures the desired state)
   4. etcd (database)
2. Worker Nodes
   1. Kublet (client agent that communicates with the master node)
   2. Container Runtime (containerd)
   3. kube-proxy (networking)

## k8s core objects

- **Pod** - smallest deployable unit, it can contain one or more containers
- **Deployment** - manages the replicas of pods, rolling updates
- **Service** - exposes the pods both internally and externally
- **CongfigMaps/Secrets** - stores the secrets
- **Ingres** - Exposes Servcies via http/https

## Setup Minikube

### Install Chocolatey if not installed

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; `
[System.Net.ServicePointManager]::SecurityProtocol = `
[System.Net.ServicePointManager]::SecurityProtocol -bor 3072; `
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

### Install Minikube

```powershell
choco install minikube -y
```

### Install kubectl

```powershell
choco install kubernetes-cli -y
```
