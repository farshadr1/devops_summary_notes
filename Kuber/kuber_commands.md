
## Usefull link for kuber yaml configs

https://k8s-examples.container-solutions.com/examples/Pod/Pod.html
https://k8syaml.com/


##

```bash
# run a pod
kubectl run nginx --image=nginx:1.27.1

# See all pods in default namespace
kubectl get pods

# see more information(image ip, version ,condition, volume, events)
kubectl describe pod nginx

# run an interactive pod
kubectl run -it alpine --image:alpine:3.20 sh

# current running instant logs
kubectl logs nginx

# Delete the pod
kubectl delete pod nginx
```

## Generating Kubernetes Manifests with kubectl
generate Kubernetes YAML without actually creating the resource
`--dry-run=client -o yaml`

for example:
```bash
# This doesn't create the deployment.
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml
```

## kubectl diff
kubectl diff shows what would change in your cluster if you applied a manifest, without actually changing anything. It compares your local files against the live state on the API server.

```bash
# Diff a single file
kubectl diff -f deployment.yaml

# Diff a directory of manifests
kubectl diff -f ./manifests/
```

## services(svc)
with define svc, if pod ip changed(recreated) we can reach the pod, or also with the pod name.

there are two type:
    - ClusterIP: internal connetion
    - NodePort: external connection

```bash
## define new svc
kubectl expose pod nginx --type=NodePort --port=80

# see all svc and ClusterIP
kubectl get service
```