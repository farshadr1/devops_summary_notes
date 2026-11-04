# Kubernetes ConfigMap – Best Practices Guide

## 1. What is a ConfigMap?

A Kubernetes ConfigMap is an API object used to store non-sensitive configuration data as key-value pairs.

It allows you to separate application configuration from container images.

Typical use cases:

- Application environment variables
- Application configuration files
- Database connection settings (non-sensitive values)
- Feature flags
- Logging configuration
- Application runtime settings

> Important: Never store passwords, API tokens, certificates, or other sensitive information in ConfigMaps. Use Kubernetes Secrets or an external secrets manager instead.

## 2. Creating a ConfigMap

There are several ways to create a ConfigMap.

### Method 1: Using YAML (Recommended)

Create `configmap.yaml`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-app-config
  namespace: production
  labels:
    app: my-app
data:
  APP_ENV: "production"
  LOG_LEVEL: "info"
  SERVER_PORT: "8080"
  FEATURE_ENABLED: "true"
```

Apply it:

```bash
kubectl apply -f configmap.yaml
```

Check the ConfigMap:

```bash
kubectl get configmaps -n production
kubectl describe configmap my-app-config -n production
kubectl get configmap my-app-config -n production -o yaml
```

### Method 2: Using kubectl

```bash
kubectl create configmap my-app-config \
  --from-literal=APP_ENV=production \
  --from-literal=LOG_LEVEL=info \
  --from-literal=SERVER_PORT=8080 \
  -n production
```

### Method 3: From a configuration file

Example `application.properties`:

```properties
app.environment=production
app.log.level=info
app.server.port=8080
```

Create the ConfigMap:

```bash
kubectl create configmap my-app-config \
  --from-file=application.properties \
  -n production
```

## 3. Using ConfigMap in a Deployment

There are three common approaches.

### A. Inject all ConfigMap values as environment variables

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  namespace: production
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: nginx:1.27
          envFrom:
            - configMapRef:
                name: my-app-config
```

Advantages:

- Simple configuration
- Easy to maintain
- No need to define every environment variable individually

Disadvantages:

- All ConfigMap keys are injected
- Environment variables are not updated automatically in running containers when the ConfigMap changes

### B. Inject selected keys as environment variables

Use this approach when your application only needs specific configuration values.

```yaml
env:
  - name: APP_ENV
    valueFrom:
      configMapKeyRef:
        name: my-app-config
        key: APP_ENV

  - name: LOG_LEVEL
    valueFrom:
      configMapKeyRef:
        name: my-app-config
        key: LOG_LEVEL
```

> Best practice: Prefer explicit key references when you want tight control over which configuration values an application receives.

### C. Mount ConfigMap as a volume

This is useful when applications expect configuration files.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  namespace: production
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: nginx:1.27
          volumeMounts:
            - name: app-config
              mountPath: /etc/my-app
              readOnly: true
      volumes:
        - name: app-config
          configMap:
            name: my-app-config
```

The ConfigMap keys become files inside `/etc/my-app`.

> Important: Mounted ConfigMap volumes are updated eventually when the ConfigMap changes, but your application must reload the configuration to use the new values.

Do not use `subPath` mounts if you expect automatic ConfigMap volume updates. A `subPath` mount does not receive those updates.

## 4. ConfigMap Best Practices

### 4.1 Separate configuration by environment

Avoid using the same configuration for development, staging, and production.

Recommended structure:

```text
kubernetes/
├── base/
│   ├── deployment.yaml
│   └── service.yaml
│
└── overlays/
    ├── dev/
    │   └── configmap.yaml
    ├── staging/
    │   └── configmap.yaml
    └── production/
        └── configmap.yaml
```

Kustomize is a good solution for managing environment-specific configurations.

### 4.2 Never store sensitive information

Bad practice:

```yaml
data:
  DB_PASSWORD: "my-secret-password"
  API_TOKEN: "123456"
```

Better practice:

```yaml
data:
  DB_HOST: "postgres.production.svc.cluster.local"
  DB_PORT: "5432"
```

Store passwords and tokens in Kubernetes Secrets or an external secrets manager.

Remember that Kubernetes Secrets also require appropriate RBAC and encryption-at-rest configuration.

### 4.3 Use meaningful names

Good examples:

```text
payment-service-config
notification-service-config
frontend-config
backend-config
```

Avoid generic names such as:

```text
config
my-config
settings
```

### 4.4 Keep ConfigMaps small

Kubernetes limits an individual ConfigMap to 1 MiB.

Best practices:

- Avoid storing large files
- Separate unrelated application configurations
- Use object storage or another configuration distribution mechanism for large configuration files

### 4.5 Use immutable ConfigMaps when appropriate

For configuration that should never change after creation:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-app-config-v1
  namespace: production
immutable: true
data:
  APP_ENV: "production"
  LOG_LEVEL: "info"
```

Advantages:

- Prevents accidental modifications
- Improves configuration consistency
- Makes configuration changes explicit

To update configuration, create a new ConfigMap and update the Deployment reference.

### 4.6 Restart Pods when environment variables change

Environment variables are only populated when a container starts.

Updating the ConfigMap does not update existing container environment variables.

You can trigger a rollout:

```bash
kubectl rollout restart deployment/my-app -n production
```

For automated GitOps deployments, consider using Kustomize's ConfigMap generator or a configuration checksum in the Deployment Pod template.

### 4.7 Use RBAC to restrict access

ConfigMaps are not secret storage, but they can contain internal application details.

Restrict access using Kubernetes RBAC.

Example read-only access:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: configmap-reader
  namespace: production
rules:
  - apiGroups: [""]
    resources: ["configmaps"]
    verbs: ["get", "list", "watch"]
```

Bind the Role only to the appropriate users or service accounts.

## 5. Updating a ConfigMap

Update your YAML file and apply it:

```bash
kubectl apply -f configmap.yaml
```

Inspect changes:

```bash
kubectl describe configmap my-app-config -n production
```

For applications using environment variables, restart the Deployment:

```bash
kubectl rollout restart deployment/my-app -n production
```

Check rollout status:

```bash
kubectl rollout status deployment/my-app -n production
```

## 6. Troubleshooting

| Problem | Possible solution |
| --- | --- |
| ConfigMap not found | Check namespace and name |
| Environment variable missing | Check key name and envFrom/env configuration |
| New configuration not applied | Restart Pods if using environment variables |
| Mounted file not updated | Check mount type and application reload behavior |
| Pod fails to start | Inspect ConfigMap references and Pod events |

Useful commands:

```bash
kubectl get cm -A
kubectl describe cm my-app-config -n production
kubectl get pods -n production
kubectl describe pod <pod-name> -n production
kubectl logs <pod-name> -n production
kubectl rollout status deployment/my-app -n production
```

## 7. Recommended Production Architecture

A practical production setup:

```text
                 Git Repository
                       |
                       |
                Kubernetes Manifests
                       |
                       |
                  Kustomize
                       |
          +------------+------------+
          |            |            |
         DEV         STAGING     PRODUCTION
          |            |            |
       ConfigMap    ConfigMap    ConfigMap
          |            |            |
       Deployment  Deployment   Deployment
          |            |            |
       Application Application  Application
```

Recommended approach:

1. Store ConfigMap definitions in Git.
2. Use Kustomize to manage environment-specific configurations.
3. Keep secrets separate from ConfigMaps.
4. Apply changes through a CI/CD or GitOps workflow.
5. Use configuration versioning or checksum-based rollouts.
6. Monitor application behavior after configuration changes.
7. Restrict ConfigMap access through RBAC.

## 8. Quick Best-Practice Checklist

- Keep application configuration outside container images
- Use declarative YAML instead of manual production changes
- Separate configurations by environment
- Never store sensitive values in ConfigMaps
- Use descriptive ConfigMap names
- Keep ConfigMaps below the 1 MiB limit
- Use `envFrom` for simple bulk injection
- Use `configMapKeyRef` for explicit environment variables
- Use volume mounts for configuration files
- Understand how ConfigMap updates affect running Pods
- Use immutable ConfigMaps when appropriate
- Use Kustomize or Helm for configuration management
- Restrict access through RBAC
- Automate configuration updates and Pod rollouts
- Keep configuration changes version-controlled

## 9. Official Kubernetes Documentation

- [ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/)
- [ConfigMap Tasks](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)
- [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/)
- [Kustomize](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/)
- [RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)

## 10. Final Recommendation

For production Kubernetes environments, my recommended approach is:

**ConfigMap + Kustomize + GitOps + Automated Rollout**

- ConfigMap: stores non-sensitive application configuration
- Kustomize: manages environment-specific variations
- GitOps: keeps configuration declarative and reviewable
- Automated rollout: ensures changes are applied safely and consistently

    - Kustomize: manages environment-specific configuration.
    - GitOps: tracks and deploys configuration changes.
    - Automated Rollout: ensures Pods receive updated configuration.
    
This approach provides better maintainability, traceability, consistency, and operational reliability.