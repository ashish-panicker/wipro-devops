# Introduction to Helm

Think of **Kubernetes** like an operating system for your cluster. You can deploy applications (like MySQL, WordPress, Nginx) on it.
But here’s the problem:

* Each app needs **lots of YAML files** (Deployment, Service, ConfigMap, Ingress, Secrets, etc.).
* Writing and maintaining them manually is hard and repetitive.

**Helm** is like a **package manager for Kubernetes** (similar to `apt` on Ubuntu or `npm` for Node.js).
It lets you install applications on Kubernetes with **just one command** instead of writing dozens of YAML files.

Example:

```bash
helm install my-nginx bitnami/nginx
```

This single command installs Nginx with all necessary YAML already packaged.

---

## Advantages of Helm

1. **Simplifies deployments** – no need to manage multiple YAML files manually.
2. **Reusability** – share and reuse pre-built application packages.
3. **Version control** – you can install a specific app version, upgrade, or roll back easily.
4. **Consistency** – ensures the same configuration is deployed across environments (dev, test, prod).
5. **Community support** – thousands of ready-to-use Helm charts are available in public repositories.

---

## Key Concepts in Helm

1. **Helm Chart**

   * A **Helm chart** is like a recipe for deploying an application on Kubernetes.
   * It contains all the Kubernetes YAML files bundled together, with templates and configurations.
   * Example: `nginx-chart` contains `deployment.yaml`, `service.yaml`, etc.

2. **Release**

   * When you install a chart on your cluster, Helm creates a **release**.
   * You can have multiple releases of the same chart (e.g., `nginx-dev`, `nginx-prod`).

3. **Repository**

   * A Helm repository is a collection of charts, just like `npm registry` or `PyPI`.
   * Example: **Bitnami Helm repo** has charts for MySQL, Nginx, WordPress, etc.

---

## Installing Helm

### On Linux / Mac

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

### On Windows (using Chocolatey)

```bash
choco install kubernetes-helm
```

Verify installation:

```bash
helm version
```

---

## Basic Helm Commands

1. **Add a repository**

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

2. **Search for a chart**

```bash
helm search repo nginx
```

3. **Install an application**

```bash
helm install my-nginx bitnami/nginx
```

4. **List installed releases**

```bash
helm list
```

5. **Upgrade a release**

```bash
helm upgrade my-nginx bitnami/nginx --set service.type=LoadBalancer
```

6. **Uninstall a release**

```bash
helm uninstall my-nginx
```

---

✅ **Quick Analogy:**

* **Chart** = recipe book for an app
* **Release** = dish you prepared from that recipe
* **Repository** = library of recipe books

---

### Sample mini-chart

**Create a chart**

```bash
helm create mini-api
```

**Installing the chart**

```bash
helm install demo ./mini-api
```

**Verify**

```bash
helm list
```

**Uninstall**

```bash
helm uninstall demo
```