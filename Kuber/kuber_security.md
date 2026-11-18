
## Role-Based Access Control (RBAC)

see all roles:
`kubectl get clusterrole`

see specific access of a role for example admin:
`kubectl get clusterrole admin -o yaml`

Generate ssl key for the user(private key)
`openssl genrsa -out alice.key 2048`

Create Certificate Signed Request certificate (/CN:CommonName, /O=GroupName)
`openssl req -new -key alice.key -out alice.csr -subj "/CN=alice/O=admin"`

```yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: alice
spec:
  request: <the .csr content in base64 format>
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 86400  # one day
  usages:
    - client auth
```
approve certificate
`kubectl certificate approve alice`

Check
`kubectl get certificatesigningrequest`

get certificate (public key)
`kubectl get csr alice -o jsonpath={.status.certificate} | base64 -d > alice.crt`

Create Role config
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: pod-reader
rules:
  - apiGroups: ['']
    resources: ['pods']
    verbs: ['get', 'list']
```

Create Role Binding resource:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  namespace: dev
  name: pod-reader
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
subjects:
  - kind: User
    name: bob
    apiGroup: rbac.authorization.k8s.io
```
switch to user:
`kubectl config use-context bob` 

### ClusterRoles
A Role defines permissions within a specific namespace, whereas a ClusterRole defines permissions globally across the entire cluster.

ClusterRole config. 
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: pod-admin
rules:
  - apiGroups: ['']
    resources: ['pods', 'pods/log', 'pods/exec', 'pods/attach', 'deployments', 'deployments/scale']
    verbs: ['*']
```

Create ClusterRole Binding resource:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: pod-admin
roleRef:
  kind: ClusterRole
  name: pod-admin
  apiGroup: rbac.authorization.k8s.io
subjects:
  - kind: Group
    name: admin
    apiGroup: rbac.authorization.k8s.io
```

### ServiceAccount
When is it appropriate to use a Service Account identity instead of a standard User identity?  
When providing an identity for processes running inside a Pod to communicate with the API Server. Service Accounts are namespaced, managed by Kubernetes, and their tokens are automatically mounted.

## ingress and egress
- Ingress governs how external users or systems reach services inside a Kubernetes cluster.

- Egress defines how internal services access systems outside the cluster, such as APIs, databases or third-party tools. Kubernetes allows unrestricted outbound traffic by default.

### Ingress compared to Kubernetes service types 
Here are some of the most common service types:

  -*ClusterIP* exposes services only within the cluster. It’s useful for internal service-to-service communication but can’t be accessed externally.

  -*NodePort* opens a specific port on every node in the cluster to expose a service externally. While simple, it’s inflexible and exposes node-level infrastructure directly, which poses operational and security challenges.

  -*LoadBalancer* provisions a cloud provider-managed load balancer for each service. This works well for external access but doesn’t scale efficiently, as each new service requires its own load balancer, which drives up complexity and cost.

  -*Gateway API* acts as an API gateway and provides advanced, flexible and extensible traffic routing and policy management beyond traditional Ingress resources.

By comparison, the Ingress resource allows multiple services to be accessed through a single external IP address by using smart routing rules, based on domain names or paths. It centralizes HTTP(S) handling and traffic control, making it a strong choice for production workloads.