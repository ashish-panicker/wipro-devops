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

## Key componenets

- **etcd** - The database, a distributed, consistent key value pair database. Stores all configuration.
- **kube-apiserver API Server** - Entry point into k8s control plane. Exposes api via http/https.
- **kube-schduler** - Assigns pods to nodes based on the reource availability.
- **kube-controller-manager** - A set of controllers running in the control plane. Ensure the desired state of deployments
  - Node controller, tracks node health
  - Deployment controller, tracks replica sets
  - HPA Controller, tracks scaling
- **Kubelet** - Node agent which runs on every node
- **kube-proxy** - Runs on every node, manages the networking rules
- **kubectl** - CLI client tool

## StatefulSet

A k8s controller responsible for deploying and managing stateful applications. Unlike deployments, which are stateless, 
Statufulset ensures that every pod has:
- Stable unique networking identities
- Persistent storage, each pod has it's own `PersistentVolumeClaim`, so the data will survie pod recreation
- Ordered Deployment & Scaling, pods are created one at a time
- Ordered termination & rolling updates

### Usecases

- Run databases
- Distributed systems
- Any application that needs persistent storage and ordered deployment