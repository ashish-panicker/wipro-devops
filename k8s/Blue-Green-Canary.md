# Deployment Stratergies

## Blue Green Deployment

Maintain two environments
- Blue -> Live
- Green -> New

```yaml
# deployment-blue.yam
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-blue
  labels:
    app: nginx
    version: blue
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
      version: blue
  template:
    metadata:
      labels:
        app: nginx
        version: blue
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```

```yaml
# green-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-green
  labels:
    app: nginx
    version: green
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
      version: green
  template:
    metadata:
      labels:
        app: nginx
        version: green
    spec:
      containers:
      - name: nginx
        image: nginx:1.23  # newer version
        ports:
        - containerPort: 80
```

```yaml
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: ClusterIP
  selector:
    app: nginx
    version: blue   # Initially pointing to BLUE
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

> Switch netween blue and green
```bash
kubectl patch svc nginx-service -p '{"spec": {"selector": {"app": "nginx", "version": "green"}}}'
```

## Canary Deployment

- Gradually release the new version to a small set of the users, genereally 10%
- Both old and new versions run at the same time
- Traffic will be routed 90% goes to the old app where as the rest goes to the new app

```bash
kubectl scale deployment nginx-canary --replicas=1
kubectl scale deployment nginx-stable --replicas=3
```