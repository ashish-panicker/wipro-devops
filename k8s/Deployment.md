# Deploy nginx in k8s

## Basic  k8s cluster commands to check cluster is running and select the correct cluster

```bash
# Shows the kubeconfig contexts defined on your machine. 
# Active context will be marked as *
kubectl config get-contexts

# Use to switch between contexts
kubectl config use-context docker-desktop

# List all nodes
kubectl get nodes

# If you want more info about nodes
# -o wide adds more info like External, Internal Ip
# Aprt from wide you can also use yaml, json
kubectl get nodes -o wide

kubectl get nodes -o yaml
```

## Deploying nginx via commands

**Create a namespace**

```bash
# Namespaces allows us to partition cluster resources
kubectl create namespace playground
```

**Update the context to use the new namespace as default**

```bash
# This allows us to skip passing namespace via -n switch
kubectl config set-context --current --namespace=playground

# To list all namespaces
kubectl get ns

# View current config
kubectl config view
```

**Create deployment and check rollout**

```bash
# kubectl create deployment <name> --image <image-name>
kubectl create deployment nginx --image nginx:latest

# Rollout the deployment
kubectl rollout status deployment/nginx

# To verify 
kubectl get deploy,rs,pods -o wide

# Scale the deployment 
kubectl scale deployment nginx --replicas=3
```

**Expose nginx**

```bash
# We will use NodePort, this will create a service
kubectl expose deployment nginx --type=NodePort --port=80 --target-port=80

# To fetch the services
kubectl get svc

kubectl get svc <service-name>

# Using Loadblancer
# Browse to http://localhost:8888 to test
kubectl expose deployment nginx --type=LoadBalancer --name=nginx-lb --port=8888 --target-port=80
```

**Rolling Update**

```bash
# Simulate an image change
kubectl set image deployment/nginx nginx=nginx:1.29.1
```

**Clean up**

```bash
# To delete a service
kubectl delete service nginx

# To delete a deployment
kubectl delete deployment nginx

# To delete all
kubectl delete deployments --all
kubectl delete services --all
```

**Deploying from yaml files**

```bash
kubectl apply -f <path-to-file.yaml>
```