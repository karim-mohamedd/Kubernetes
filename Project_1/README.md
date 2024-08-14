
---

# Kubernetes Deployment Project

This project demonstrates how to:

1. Dockerize two applications.
2. Push Docker images to Docker Hub.
3. Create Kubernetes namespaces and resource quotas.
4. Deploy applications in the namespaces with enforced quotas.
5. Update one application using a rolling update.
6. Create services for deployments.
7. Configure Ingress to access the applications externally using domain names.

## Prerequisites

- **Docker**: To build and push Docker images.
- **Kubectl**: To interact with the Kubernetes cluster.
- **Helm** (optional): For managing Kubernetes manifests (not used in this example, but helpful for more complex setups).
- **Ingress Controller**: An Ingress Controller like NGINX must be installed in the Kubernetes cluster.

## Step 1: Dockerize the Applications

Create Dockerfiles for two applications. 

**Dockerfile for `app1` (with new feature):**
```Dockerfile

FROM python:3.9-slim

WORKDIR /app

COPY app1.py/ /app/


CMD ["python", "app.py"]
```

**Dockerfile for `app2` (with old feature):**
```Dockerfile
# Dockerfile for app2 with old feature
FROM python:3.9-slim

WORKDIR /app

COPY app2.py/ /app/


CMD ["python", "app.py"]
```

### Build and Push Docker Images

```bash
# Build images
docker build -t yourdockerhubusername/app1:latest -f Dockerfile.app1 .
docker build -t yourdockerhubusername/app2:latest -f Dockerfile.app2 .

# Push images
docker push yourdockerhubusername/app1:latest
docker push yourdockerhubusername/app2:latest
```

## Step 2: Create Namespaces and Resource Quotas

Create YAML files for namespaces and quotas.

**Namespaces (`namespaces.yaml`):**
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: ns1
---
apiVersion: v1
kind: Namespace
metadata:
  name: ns2
```

**Quotas (`quotas.yaml`):**
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: deployment-quota-ns1
  namespace: ns1
spec:
  hard:
    deployments.apps: "1"
---
apiVersion: v1
kind: ResourceQuota
metadata:
  name: deployment-quota-ns2
  namespace: ns2
spec:
  hard:
    deployments.apps: "1"
```

Apply these configurations:
```bash
kubectl apply -f namespaces.yaml
kubectl apply -f quotas.yaml
```

## Step 3: Create Deployments

**Deployment for `app1` in `ns1` (`app1-deployment.yaml`):**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1-deployment
  namespace: ns1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app1
  template:
    metadata:
      labels:
        app: app1
    spec:
      containers:
      - name: app1
        image: yourdockerhubusername/app1:latest
        ports:
        - containerPort: 80
```

**Deployment for `app2` in `ns2` (`app2-deployment.yaml`):**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app2-deployment
  namespace: ns2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app2
  template:
    metadata:
      labels:
        app: app2
    spec:
      containers:
      - name: app2
        image: yourdockerhubusername/app2:latest
        ports:
        - containerPort: 80
```

Apply the deployments:
```bash
kubectl apply -f app1-deployment.yaml
kubectl apply -f app2-deployment.yaml
```

### Test Resource Quota Enforcement

Attempt to create an additional deployment in `ns1` to verify that the quota is enforced.

**Extra Deployment (`extra-deployment.yaml`):**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: extra-deployment
  namespace: ns1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: extra
  template:
    metadata:
      labels:
        app: extra
    spec:
      containers:
      - name: extra
        image: busybox
        ports:
        - containerPort: 80
```

Apply it:
```bash
kubectl apply -f extra-deployment.yaml
```

You should see an error indicating that the quota has been exceeded.

## Step 4: Rolling Update for `app1`

Assuming you have a new version of `app1` image (`yourdockerhubusername/app1:new-feature`), update the deployment:

**Update Deployment (`app1-update.yaml`):**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1-deployment
  namespace: ns1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app1
  template:
    metadata:
      labels:
        app: app1
    spec:
      containers:
      - name: app1
        image: yourdockerhubusername/app1:new-feature
        ports:
        - containerPort: 80
```

Apply the update:
```bash
kubectl apply -f app1-update.yaml
```

## Step 5: Create Services

**Service for `app1` in `ns1` (`app1-service.yaml`):**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: app1-service
  namespace: ns1
spec:
  selector:
    app: app1
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

**Service for `app2` in `ns2` (`app2-service.yaml`):**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: app2-service
  namespace: ns2
spec:
  selector:
    app: app2
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

Apply the services:
```bash
kubectl apply -f app1-service.yaml
kubectl apply -f app2-service.yaml
```

## Step 6: Create Ingress Resources

**Ingress for `app1` in `ns1` (`app1-ingress.yaml`):**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app1-ingress
  namespace: ns1
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: app1.project.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app1-service
            port:
              number: 80
```

**Ingress for `app2` in `ns2` (`app2-ingress.yaml`):**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app2-ingress
  namespace: ns2
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: app2.project.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app2-service
            port:
              number: 80
```

Apply the Ingress resources:
```bash
kubectl apply -f app1-ingress.yaml
kubectl apply -f app2-ingress.yaml
```

## Step 7: Configure DNS

Ensure that `app1.project.com` and `app2.project.com` point to the external IP address of your Ingress Controller. This can usually be done through your DNS provider’s management console. 

- Obtain the external IP of the Ingress Controller:

  ```bash
  kubectl get service <ingress-controller-service-name> --namespace <ingress-namespace>
  ```

- Update DNS records to point to this IP address.

## Conclusion

You have now Dockerized two applications, pushed them to Docker Hub, set up Kubernetes namespaces with resource quotas, deployed the applications, updated one using a rolling update, created services, and exposed them via Ingress with domain names.

If you encounter any issues or need further assistance, please refer to the Kubernetes and Docker documentation or seek help from the community.

---

This `README.md` file provides a comprehensive guide for setting up your Kubernetes environment and should help users understand each step of the process. Adjust any details based on your specific setup or requirements.