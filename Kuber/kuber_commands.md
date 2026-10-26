
## Useful link for kuber yaml configs

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
kubectl delete --force pod nginx

# execute costume command from container
kubectl exec -it pod-name -- sh

# filter with Labels
kubectl get pods -l "tier=backend,status!=unstable"

# Show apiVersion of Resources
kubectl api-resources

# Delete all resources
kubectl delete all --all
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

**commands**
```bash
# apply new Deployment
kubectl apply -f nginx-deployment.yaml

# apply multiple resources
kubectl apply -f nginx-deployment-directory

# delete resources
kubectl delete -f nginx-deployment-directory
kubectl delete --force -f nginx-deployment-directory

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

# restart rollout
kubectl restart undo deployment nginx-deployment

# annotate the deployment
kubectl annotate deployment nginx-deployment "kubernetes.io/change-cause": "Update nginx to tag 1.27.0"

# imperative Scale Deployment
kubectl scale deploy nginx-deployment --replicas=5
```
## name spaces
In Kubernetes, namespaces provide a mechanism for isolating groups of resources within a single cluster. Namespaces cannot be nested inside one another and each Kubernetes resource can only be in one namespace.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

- for get resources in a specific name space ,use `-n dev` or `--all-namespaces`
If we wont every time assert this can do:
`kubectl config set-context --current --namespace=dev`
to check current namespace:
`kubectl config view`

- Pods Between two different namespace can communicate with the Fully Qualified Domain Name (FQDN) : `<service-name>.<namespace>.svc.cluster.local`

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
> Labels are not unique, and multiple resources can have same label like `app: api-backend`.

example:ClusterIP
```yaml
apiVersion: v1
kind: Service
metadata:
  name: api-clusterIp
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

- When delete and recreate a Deployment, the Pod IP addresses change. When delete and recreate the Service itself, the ClusterIP address changes. The most robust way for connect is using Service's name.

example:Nodeport
access to node.
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

## Startup, Liveness, and Readiness Probes

|Probe Type| Core Question| Action on Failure| Ideal Use Case|
|----------|--------------|------------------| --------------|
|**startupProbe**| Is the application done initializing?| Kills and restarts the container.|Slow-starting legacy apps, heavy migrations, or cache warming.|
|**readinessProbe**|	Can this container accept user traffic right now?|	Removes Pod from Service endpoints (no traffic).|	Temporary downtime (e.g., loaded buffers, database reconnects).|
|**livenessProbe**|	Is the container stuck, deadlocked, or broken?|	Kills and restarts the container.|	Catching deadlocks, fatal crashes, or infinite loops.|

example:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: application
        image: nginx:alpine
        ports:
        - containerPort: 8080

        # 1. STARTUP PROBE: Gives the app up to 2 minutes to start up
        startupProbe:
          httpGet:
            path: /healths/startup
            port: 8080
          periodSeconds: 10      # Check every 10s
          failureThreshold: 12   # Allow 12 failures (10s * 12 = 120s budget)

        # 2. LIVENESS PROBE: Only evaluates after startupProbe passes
        livenessProbe:
          httpGet:
            path: /healths/live
            port: 8080
          periodSeconds: 15      # Check every 15s
          timeoutSeconds: 2      # Must respond within 2s
          failureThreshold: 3    # Restart after 3 consecutive failures

        # 3. READINESS PROBE: Only evaluates after startupProbe passes
        readinessProbe:
          httpGet:
            path: /healths/ready
            port: 8080
          periodSeconds: 5       # Aggressive checking for traffic routing
          timeoutSeconds: 2      # Must respond within 2s
          failureThreshold: 2    # Remove from Service endpoints quickly if broken
          successThreshold: 1    # Put back in rotation after 1 success
```

## Pods Fail Status

Fault Message | issue 
------------- | ----
**ImagePullBackOff**: | Image is not accessible.
**OOMKilled**: | Out Of Memory, Image Killed because resources Limit
**CrashLoopBackOff** : | multiple Crashed and currently waiting to restart again

##