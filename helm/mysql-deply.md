# Deploy MySQL and phpMyAdmin using Helm

**Add the repository**

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

**Install Mysql**

```bash
# Run these commands in git bash prompt
helm install my-mysql bitnami/mysql \
  --set auth.rootPassword=rootpassword123 \
  --set auth.database=mydb \
  --set primary.persistence.enabled=false
```

- `auth.rootPassword` → set your root password.
- `auth.database` → creates a default database.
- `primary.persistence.enabled=false` → disables persistent volume (in production, enable persistence).

**Check status**

```bash
kubectl get pods
kubectl get svc
```

**Install phpMyAdmin**

```bash
helm install my-phpmyadmin bitnami/phpmyadmin \
  --set db.host=my-mysql
```
- `db.host=my-mysql` → points phpMyAdmin to the MySQL service we installed above.

**Setup the load balancer service**

```bash
helm upgrade my-phpmyadmin bitnami/phpmyadmin --set service.type=LoadBalancer

# Portfowarding is an alternative as well
# Check http://localhost:7777
kubectl port-forward svc/my-phpmyadmin 7777:80
```

**Check external IP**

```bash
kubectl get svc my-phpmyadmin
```