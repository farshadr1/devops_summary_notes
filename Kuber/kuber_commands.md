
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

# logs
kubectl logs nginx

# Delete the pod
kubectl delete pod nginx

```

## services(svc)
with define svc, if pod ip changed(recreated) we can reach the pod, or also with the pod name.

```bash
## define new svc
kubectl expose pod nginx --type=NodePort --port=80

# see all svc and ClusterIP
kubectl get service
```