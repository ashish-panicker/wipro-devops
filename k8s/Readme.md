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

## What is Horizontal Deployment?

Horizontal deployment refers to scaling your application **out** by deploying multiple instances (replicas) of the same service or application across nodes/servers.

Instead of making a single server more powerful (vertical scaling), you **add more servers/containers/pods** to handle the load.

### Key Characteristics

1. **Multiple Instances** – Same app is deployed in multiple copies (replicas).
2. **Load Balancing** – A load balancer or service distributes requests across all running instances.
3. **High Availability** – If one instance crashes, others continue serving requests.
4. **Elastic Scalability** – Easier to add/remove instances dynamically (cloud/Kubernetes auto-scaling).

### Example in Kubernetes

Suppose you deploy an Nginx app:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3   # 🔹 horizontal deployment - 3 instances
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```

* Here, **replicas: 3** means Kubernetes will horizontally deploy **3 pods** of the same Nginx container.
* A **Service** (e.g., LoadBalancer or ClusterIP) will distribute traffic across them.

### Horizontal vs Vertical Deployment

| **Aspect**      | **Horizontal (Scale-Out)**                | **Vertical (Scale-Up)**                         |
| --------------- | ----------------------------------------- | ----------------------------------------------- |
| Approach        | Add more instances                        | Add more resources (CPU/RAM) to single instance |
| Fault Tolerance | High (redundancy)                         | Low (single point of failure)                   |
| Cost            | Flexible (pay as you grow)                | Expensive after certain limits                  |
| Complexity      | Needs load balancing, distributed systems | Simpler, but limited by hardware                |

So, when you hear **horizontal deployment**, think of it as **cloning your app across multiple servers/pods and balancing the load**.

## Kubernetes Services & Ingress – Deep Dive

Kubernetes **Services** provide stable networking and service discovery for Pods. Ingress builds on top of them for smarter HTTP/HTTPS routing.

### **1. ClusterIP (Default Service Type)**

* **What it does**: Creates an internal cluster-only IP.
* **When to use**: Pod-to-pod communication inside the cluster (e.g., frontend talking to backend).
* **How it works**:

  * Kubernetes allocates a **virtual IP (VIP)** from the cluster range.
  * Other pods/services inside the cluster can reach it, but it’s not accessible outside.
* **Example**:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: myapp
  ports:
    - port: 80
      targetPort: 8080
  type: ClusterIP
```

---

### **2. NodePort**

* **What it does**: Exposes the service on **each Node’s IP** at a static port (default range: `30000–32767`).
* **When to use**: For quick debugging or exposing services in bare-metal clusters without LoadBalancer.
* **How it works**:

  * Assigns a port on all nodes.
  * Traffic coming to `<NodeIP>:<NodePort>` is forwarded to the service.
* **Example**:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service
spec:
  type: NodePort
  selector:
    app: myapp
  ports:
    - port: 80
      targetPort: 8080
      nodePort: 31000
```

---

### **3. LoadBalancer**

* **What it does**: Integrates with **cloud provider’s load balancer** (AWS ELB, Azure LB, GCP LB).
* **When to use**: Exposing production services externally with managed load balancing.
* **How it works**:

  * Cloud creates an external LoadBalancer.
  * The LB forwards requests to the NodePort service automatically.
* **Example**:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-loadbalancer-service
spec:
  type: LoadBalancer
  selector:
    app: myapp
  ports:
    - port: 80
      targetPort: 8080
```

On **minikube/docker desktop/kind**, LoadBalancer often falls back to NodePort because no cloud LB exists.

### **4. ExternalName**

* **What it does**: Maps a Service name to an **external DNS hostname**.
* **When to use**: When you want pods inside the cluster to call an external service (like `mysql.example.com`).
* **How it works**:

  * Kubernetes doesn’t proxy traffic. Instead, it creates a **CNAME record in DNS**.
* **Example**:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-external-service
spec:
  type: ExternalName
  externalName: database.example.com
```

### **5. Ingress**

* **What it does**: Acts as an **HTTP/HTTPS router** sitting in front of multiple services.

* **When to use**: To route based on hostname/path (e.g., `/api → backend`, `/ → frontend`).

* **How it works**:

  * You need an **Ingress Controller** (e.g., NGINX Ingress Controller, Traefik, HAProxy).
  * Works with services of type **ClusterIP/NodePort/LoadBalancer**.
  * Provides:

    * Path-based routing (`/api`, `/admin`, etc.)
    * Host-based routing (`foo.com`, `bar.com`)
    * TLS termination (HTTPS).

* **Ingress Example**:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: myapp.local
      http:
        paths:
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: my-backend-service
                port:
                  number: 8080
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-frontend-service
                port:
                  number: 80
```

### **How They Work Together**

* **ClusterIP** → Internal communication only.
* **NodePort** → Expose service on all node IPs.
* **LoadBalancer** → Expose service to the internet (cloud-managed).
* **ExternalName** → Redirect DNS to external services.
* **Ingress** → Smart layer-7 routing (HTTP/HTTPS), combining multiple services behind one entrypoint.

Typically:

1. Apps (Pods) expose themselves with **ClusterIP**.
2. **Ingress Controller** (itself a LoadBalancer/NodePort) routes external requests to ClusterIP services.
3. Optional: **LoadBalancer** from cloud forwards to Ingress Controller.

## Demo

A **full demo** with:

* **Deployment** (nginx pods)
* **ClusterIP Service** (internal stable service for the pods)
* **Ingress** (using NGINX Ingress Controller) to expose it via HTTP on Docker Desktop

This way, you’ll see how traffic flows **Ingress → Service (ClusterIP) → Pod (nginx)**.

### 🛠 Step 1: Create `nginx-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

**What this does**:

* Creates **2 replicas** of NGINX pods.
* Labels them with `app: nginx`.
* Exposes container **port 80** inside the pod.

### 🛠 Step 2: Create `nginx-service.yaml` (ClusterIP)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

**What this does**:

* Creates a **ClusterIP service** called `nginx-service`.
* Forwards traffic on port **80 → pods (80)**.
* Service selects pods with `app: nginx`.

### 🛠 Step 3: Install NGINX Ingress Controller (for Docker Desktop)

Since you’re on **Docker Desktop**, enable Ingress with:

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

Wait a few seconds, then check:

```sh
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
```

You should see an ingress controller **LoadBalancer service** running (on Docker Desktop, it usually maps to `localhost`).

### 🛠 Step 4: Create `nginx-ingress.yaml`

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: nginx.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: nginx-service
                port:
                  number: 80
```
**What this does**:

* Defines an Ingress named `nginx-ingress`.
* Routes all traffic for `http://nginx.local/` → **nginx-service:80**.
* Uses **Ingress Controller** (installed above) to handle routing.

### 🛠 Step 5: Apply All Files

```sh
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-service.yaml
kubectl apply -f nginx-ingress.yaml
```

### 🛠 Step 6: Update Hosts File (Windows)

Edit file:

```
C:\Windows\System32\drivers\etc\hosts
```

Add:

```
127.0.0.1   nginx.local
```

### 🛠 Step 7: Test in Browser

Open:

```
http://nginx.local/
```

You should see the **NGINX Welcome Page**.

✅ End-to-end routing path:
**Browser → Ingress Controller → Ingress Rule → ClusterIP Service → NGINX Pods**