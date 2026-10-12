
## Usefull link for kuber yaml configs

https://k8s-examples.container-solutions.com/examples/Pod/Pod.html
https://k8syaml.com/


## imperative commands for Pods

```bash
# See nodes in cluster
kubectl get nodes -o wide 
# run a pod
kubectl run nginx --image=nginx:1.27.1

# See all pods in default namespace
kubectl get pod

# See updating every 1s
kubectl get pod --watch

# see more information(image ip, version ,condition, volume, events)
kubectl describe pod nginx

# run an interactive pod
kubectl run -it alpine --image:alpine:3.20 sh

# current running instant logs
kubectl logs nginx

# Delete the pod
kubectl delete pod nginx

# execute costume command from container
kubectl exec -it pod-name -- sh
```
### Declarative run Pods
example:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
  labels:
    name: myapp
spec:
  containers:
  - name: myapp
    image: <Image>
    args:
      - 'arg1'
      - 'arg2'
    resources:
      requests:
        memory: "128Mi"
        cpu: "500m"
    ports:
      - containerPort: <Port>
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
## Deployment
The Deployment creates a new ReplicaSet with the updated Pod template. It then gradually scales up the new ReplicaSet (adding new Pods) while simultaneously scaling down the old ReplicaSet (terminating old Pods), ensuring the application remains available throughout the process.
The Deployment can Controlling Scale and Rollout strategy.

### example: Declarative Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.27.0
        ports:
        - containerPort: 80
```
- A Deployment named nginx-deployment is created, indicated by the `.metadata.name` field. This name will become the basis for the ReplicaSets and Pods which are created later
- The Deployment creates a ReplicaSet that creates three replicated Pods, indicated by the `.spec.replicas` field.
- The `.spec.selector` field select a label that is defined in the Pod template. (app: nginx)

use `kubectl apply -f nginx-deployment.yaml` to apply new Deployment.

**commands**
```bash
# check if the Deployment was created
kubectl get deployments

# Revision the history of rollout
kubectl rollout history deployment nginx-deployment

# history of certain rollout output in yaml
kubectl rollout history deployment nginx-deployment --revision=2 -o yaml

# check the Deployment rollout status
kubectl rollout status deployment nginx-deployment

# undo rollout
kubectl rollout undo deployment nginx-deployment

# annotate the deployment
kubectl annotate deployment nginx-deployment "kubernetes.io/change-cause": "Update nginx to tag 1.27.0"

# imperative Scale Deployment
kubectl scale deploy nginx-deployment --replicas=5
```

## services(svc)
with define svc, if pod ip changed(recreated) we can reach the pod, or also connect with the pod's name.  

```bash
## define new svc (imperative)
kubectl expose pod nginx --type=NodePort --port=80

# see all svc and ClusterIP
kubectl get service
```

The Service defines a `selector` in its specification. It continuously looks for all Pods in the same namespace that have matching labels: 
```yaml  
spec:
  template:
    metadata:
      labels:
```      

example:ClusterIP
```yaml
apiVersion: v1
kind: Service
metadata:
  name: api-clusterip
  labels:
    app: api-backend
spec:
  type: ClusterIP
  selector: 
    app: api-backend
  ports:
    - port: 80
      targetPort: 80
```

example:Nodeport
```yaml
...
spec:
  type: NodePort
  selector: 
    app: api-backend
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007
```

### Service Types:

| Service Type | Behavior | When to use |
| ------------ | -------- | ----------- |
| ClusterIP |- Exposes the service on an **internal**, stable cluster IP.<br>- It is only accessible within the cluster. |- Best for internal communication between microservices within the cluster.<br>- Often combined with ingresses to expose applications via HTTP and HTTPS.|
| NodePort |- Exposes the service on each Node's IP at a **static port**.<br>- If not specified, picks a port from the range 30000-32767.<br>- Accessible from outside the cluster using \<NodeIP>:\<NodePort>. |- Useful for exposing a service externally **without a load balancer**.<br>- Often for **development or testing**. |
| LoadBalancer |- Exposes the service externally using a **cloud** provider's load balancer.<br>- Provides a single external IP to reach the service. |- Ideal for production environments where external access with load balancing is required. |
| ExternalName |- Maps the service to an external DNS name, **redirecting traffic** to a service outside the cluster. |- Used to allow services within the cluster to access external services via a DNS name. |


## Pods Fail Status

- **ImagePullBackOff**: Image is not accessible.