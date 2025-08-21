# Scaling

Scaling means adjusting the number of pods, replica set, etc to handle changes in load. There are mainly
two types:
1. Horizontal Scaling
   1. Add/removes more pods
2. Vertical Scaling
   1. Modify the system resources

## Scale a deplyment manually

**webapp deployment**

```yaml
# webapp-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
  labels:
    app: webapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp
        image: nginx:latest
        ports:
        - containerPort: 80
```

```bash
# Apply the depoyment
kubectl apply -f webapp-deployment.yaml

# Verify deployment
kubectl get deploy webapp

# Scale manually
kubectl scale deployment webapp --replicas=<num>
```

## HPA - Horizontal Pod AutoScaler

Autoscaling refers to a set of rules that determines when the resources must be scaled. HPA automatically adjusts the 
number of pods based on metrics like CPU usage, memory or even custom user defined ones.


### How does HPA work

- Define min and max replicas
- Set target matrices
- k8s will monitor and automtically adjust it

```bash
# Install Metrics Server
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# Enable HPA
kubectl autoscale deployment webapp --cpu-percent=50 --min=2 --max=10

# Verify
kubectl top nodes
kubectl top pods
```

### Challenge

1. Create a Deployment `webapp` with 2 replicas
2. Deploy Metrics Server
3. Apply HPA target of CPU=50, Min=2, Max=10
4. Generate a sample load
5. Observe pod scaling
   ```bash 
   kubectl get hpa -w
   kubectl get pods -w
   ```