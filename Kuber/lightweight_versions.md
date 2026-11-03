## Minikube

Minikube is a lightweight Kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node.

### Installation

first install container runtime(Docker).

```bash
# Install
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64

# Verify installation
kubectl version
kubectl config current-context 

# stop cluster
minikube stop

# start cluster
minikube start

# Connect to node
minikube ssh
```
