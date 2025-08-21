# Probes

Kubernetes uses probes to check the health of containers inside a pod. There are three kinds:
1. **Liveness Probes** - checks if the container is alive
2. **Readiness Probes** - ready to serve
3. **Startup Probes** - Check if the container startup was successfull

**Why do we need them?**

We can route traffic to healthy pods and avoid unhealthy pods. They allow k8s to understand if a pod is alive and ready.

