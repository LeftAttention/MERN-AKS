
# Deployment Guide for MERN Applications on AKS


## Step 1: Create Dockerfiles

### HelloService

1. Navigate to the `helloService` directory.
2. Create a `Dockerfile` with the following content:

```dockerfile
FROM node:14
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]
```

### ProfileService

1. Navigate to the `profileService` directory.
2. Create a `Dockerfile` with the following content:

```dockerfile
FROM node:14
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3001
CMD ["node", "index.js"]
```

## Step 2: Build and Push Docker Images

### Build Images

Open a terminal and run the following commands to build Docker images for both services:

```bash
docker build -t <acr-name>.azurecr.io/helloservice:latest ./helloService
docker build -t <acr-name>.azurecr.io/profileservice:latest ./profileService
```

Replace `<acr-name>` with the name of your Azure Container Registry.

### Push Images to ACR

Authenticate to your ACR:

```bash
az acr login --name <acr-name>
```

Push the built images:

```bash
docker push <acr-name>.azurecr.io/helloservice:latest
docker push <acr-name>.azurecr.io/profileservice:latest
```

## Step 3: Deploy on AKS

### Create AKS Cluster

If not already created, set up an AKS cluster via Azure CLI:

```bash
az aks create --resource-group <resource-group-name> --name <cluster-name> --node-count 2 --enable-addons monitoring --generate-ssh-keys
```

### Configure kubectl

Connect your local `kubectl` to the AKS cluster:

```bash
az aks get-credentials --resource-group <resource-group-name> --name <cluster-name>
```

### Deploy Services

Deploy the applications using the Kubernetes YAML configurations created earlier:

```bash
kubectl apply -f hello-deployment.yaml
kubectl apply -f profile-deployment.yaml
```

### Verify the Deployment

Check the deployment status:

```bash
kubectl get pods
kubectl get services
```

The output will provide the details of running pods and services, including the external IP addresses if the services are exposed via LoadBalancer.
