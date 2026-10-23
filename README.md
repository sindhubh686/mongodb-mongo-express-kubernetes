# Deploy MongoDB and Mongo Express on Local Kubernetes

**Module 10: Container Orchestration with Kubernetes**

A demo project that deploys MongoDB and Mongo Express to a local Kubernetes cluster running with Docker Desktop. Kubernetes ConfigMaps keep non-sensitive configuration separate from the application manifests, while Secrets provide database credentials.

## Technologies

- Docker Desktop
- Kubernetes
- kubectl
- MongoDB
- Mongo Express
- ConfigMap and Secret
- Kubernetes Deployments and Services

## Architecture

```text
Browser (http://localhost:8081)
             |
             v
  mongo-express-service
     (LoadBalancer)
             |
             v
      Mongo Express Pod
             |
             | Kubernetes Service DNS
             v
       mongo-service
             |
             v
        MongoDB Pod

ConfigMap -> non-sensitive connection settings
Secret    -> MongoDB username and password
```

## Prerequisites

Install and start Docker Desktop, and enable Kubernetes in Docker Desktop's settings. Install `kubectl` as well.

Verify the tools:

```bash
docker --version
kubectl version --client
kubectl config get-contexts
```

Make sure Docker Desktop Kubernetes is running and select its context:

```bash
kubectl config use-context docker-desktop
kubectl get nodes
```

The node should report `Ready`.

## Project files

The filenames below are examples. Adjust the commands to match the manifests in this repository.

```text
.
├── README.md
├── mongo-secret.yaml
├── mongo-configmap.yaml
├── mongo.yaml
└── mongo-express.yaml
```

- `mongo-secret.yaml`: MongoDB credentials stored in a Kubernetes Secret.
- `mongo-configmap.yaml`: non-sensitive MongoDB connection configuration.
- `mongo.yaml`: MongoDB Deployment and Service.
- `mongo-express.yaml`: Mongo Express Deployment and Service.

## Deploy

Run these commands from the project directory. Apply only files that exist in your project; filenames and Kubernetes resource names must match your manifests.

```bash
kubectl apply -f mongo-secret.yaml
kubectl apply -f mongo-configmap.yaml
kubectl apply -f mongo.yaml
kubectl apply -f mongo-express.yaml
```

Check the resources:

```bash
kubectl get configmaps
kubectl get secrets
kubectl get deployments
kubectl get pods
kubectl get services
```

Wait until the application Pods are ready. If a Pod fails to start, inspect it:

```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

## Open Mongo Express

If the Service is named `mongo-express-service` and uses the `LoadBalancer` type with `localhost` as its external address, open:

**http://localhost:8081**

Check the actual Service configuration:

```bash
kubectl get svc mongo-express-service
```

If the browser cannot connect, inspect the Pods and logs:

```bash
kubectl get pods
kubectl logs deployment/mongo-express
```

The deployment name may differ; use the name shown by `kubectl get deployments`.

## How it works

1. Kubernetes reads the Secret and ConfigMap.
2. The MongoDB Deployment creates a Pod running the MongoDB container.
3. The MongoDB Service provides a stable internal endpoint for database traffic.
4. The Mongo Express Deployment starts the web interface and receives the connection settings and credentials.
5. The Mongo Express Service exposes the interface on the local machine.
6. Open the browser URL to manage the database through Mongo Express.

## Troubleshooting

### kubectl connects to the wrong cluster

```bash
kubectl config current-context
kubectl config use-context docker-desktop
kubectl get nodes
```

### A Pod is not starting

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

### Mongo Express cannot connect to MongoDB

Confirm that:
- The MongoDB Service name matches the hostname configured for Mongo Express.
- Secret names and keys match the references in the Deployment manifests.
- MongoDB is ready and listening on port `27017`.
- The credentials used by Mongo Express match the MongoDB credentials.

### Stop and remove the demo resources

Review the resources before deleting them. To remove resources created from these manifests:

```bash
kubectl delete -f mongo-express.yaml
kubectl delete -f mongo.yaml
kubectl delete -f mongo-configmap.yaml
kubectl delete -f mongo-secret.yaml
```

This removes the Kubernetes resources described by those files. Persistent data behavior depends on the storage configuration.

## Security notes

- Do not commit real passwords, API keys, or other credentials to GitHub.
- Kubernetes Secret values are commonly Base64-encoded; Base64 is not encryption.
- For a public repository, use a placeholder/example Secret manifest or a template and provide real credentials locally.
- Use strong credentials and appropriate access controls.
- For persistent or production workloads, configure suitable persistent storage, backups, and security settings.

## What I learned

- Starting and using a local Kubernetes cluster with Docker Desktop.
- Deploying applications with Kubernetes Deployments and Pods.
- Connecting workloads with Kubernetes Services.
- Separating non-sensitive configuration into ConfigMaps.
- Supplying credentials through Secrets.
- Inspecting and troubleshooting Kubernetes resources.

## Author

Add your name and GitHub profile here.
