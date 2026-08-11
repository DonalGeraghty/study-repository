# Kubernetes

Kubernetes is a platform for deploying and managing containerised applications across a cluster of machines. It does not build container images. Instead, it pulls existing images and keeps the resulting workloads aligned with a declared desired state.

## Why Kubernetes Is Used

Running a single container directly with Docker is straightforward. A production system may need multiple replicas, automatic recovery, service discovery, rolling releases, configuration, storage, and scheduling across many machines. Kubernetes coordinates those concerns.

Typical capabilities include:

- scheduling containers onto suitable machines;
- restarting or replacing failed workloads;
- scaling an application across multiple replicas;
- providing stable networking in front of changing containers;
- rolling out and rolling back application versions;
- injecting configuration and secrets;
- attaching persistent storage;
- declaring CPU and memory requirements.

## Cluster Architecture

A Kubernetes cluster consists of a **control plane** and one or more **worker nodes**.

```text
kubectl -> API server -> desired state stored in etcd
                         |              |
                         |              -> controllers reconcile state
                         -> scheduler assigns Pods to Nodes
                                            |
                                            -> kubelet starts containers
```

### Control Plane

| Component | Responsibility |
| :--- | :--- |
| API server | Exposes the Kubernetes API and applies authentication, authorisation, admission, and validation. |
| `etcd` | Stores the cluster's configuration and state. |
| Scheduler | Assigns unscheduled Pods to suitable nodes. |
| Controller manager | Runs reconciliation loops that move actual state towards desired state. |
| Cloud controller manager | Optionally integrates nodes, routes, and load balancers with a cloud provider. |

### Worker Node

| Component | Responsibility |
| :--- | :--- |
| `kubelet` | Ensures the containers described by assigned Pods are running. |
| Container runtime | Pulls images and runs containers through the Container Runtime Interface. |
| Pod network implementation | Connects Pods according to the Kubernetes network model. |
| Service data plane | Routes Service traffic, often through `kube-proxy` or an implementation supplied by the network plugin. |

## Core Kubernetes Objects

### Pod

A Pod is the smallest deployable Kubernetes object. It contains one or more tightly coupled containers that share networking and can share storage. Containers in the same Pod communicate through `localhost`.

Pods are disposable. Applications should not rely on a specific Pod name, IP address, or local filesystem surviving replacement.

Use multiple containers in one Pod only when they are tightly coupled and should share lifecycle and networking. Init containers perform setup before application containers start; sidecars provide supporting behaviour alongside them. Separate services normally belong in separate Pods.

### Deployment

A Deployment manages a set of identical application Pods. It uses a ReplicaSet to maintain the requested replica count and supports controlled rolling updates and rollbacks.

For a stateless application, create a Deployment rather than managing individual Pods directly.

### Other Workload Controllers

Choose a controller that matches the workload lifecycle:

| Controller | Use |
| :--- | :--- |
| `Deployment` | Replaceable, usually stateless application replicas |
| `StatefulSet` | Pods needing stable identities, ordered operations, or per-Pod storage |
| `DaemonSet` | One Pod on each selected node, often for node-level agents |
| `Job` | Finite work that must complete |
| `CronJob` | Jobs created on a schedule |

A controller does not make an application stateless or distributed safely. The application must still handle concurrency, retries, termination, and data consistency.

### Service

A Service provides a stable virtual address and DNS name for a changing group of Pods. It selects Pods by label and distributes traffic to ready endpoints.

Common Service types are:

| Type | Use |
| :--- | :--- |
| `ClusterIP` | Makes the Service reachable only inside the cluster. This is the default. |
| `NodePort` | Exposes a port on every node. It is mainly useful for simple or specialised setups. |
| `LoadBalancer` | Requests an external load balancer from a supported environment. |
| `ExternalName` | Maps the Service to an external DNS name. |

For HTTP and HTTPS routing to one or more Services, use a Gateway implementation or an Ingress controller supported by the platform. Creating an Ingress object without a controller does nothing. The Ingress API is stable but frozen; Kubernetes recommends Gateway API for new capabilities.

### Namespace

A Namespace provides a logical boundary for namespaced resources. Namespaces help organise environments, teams, and applications, but do not provide complete security isolation on their own.

### ConfigMap and Secret

A ConfigMap stores non-sensitive configuration. A Secret is intended for sensitive values. Secret data is base64-encoded by default, which is not encryption; cluster access controls and encryption at rest still matter.

## Declarative Management and Reconciliation

Kubernetes resources are usually described in YAML and submitted to the API server with `kubectl apply`. Controllers continuously compare the desired state in the API with actual cluster state and take action when they differ.

For example, if a Deployment declares two replicas and one Pod fails, its controller creates a replacement. Kubernetes is reconciling state rather than running a one-time script.

Every manifest normally has four important areas:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example
spec:
  # Desired state for this resource
```

- `apiVersion` identifies the API group and version.
- `kind` identifies the resource type.
- `metadata` contains its name, namespace, labels, and annotations.
- `spec` describes the desired state.

## Example: Deploy the Docker Application

This example deploys the `hello-docker:1.0` image created in the [Docker guide](./docker.md). A Kubernetes node must be able to pull or otherwise access the image.

For a registry-based workflow, tag and push the image first. Replace the registry name with one you can access:

```bash
docker tag hello-docker:1.0 your-registry.example/hello-docker:1.0
docker push your-registry.example/hello-docker:1.0
```

For a local development cluster, use that cluster's documented image-loading method instead. An image stored only in the host Docker Engine is not automatically available to every Kubernetes node.

### Manifest

Create `kubernetes.yaml`:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: docker-study
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-app
  namespace: docker-study
  labels:
    app.kubernetes.io/name: hello-app
spec:
  replicas: 2
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 0
      maxSurge: 1
  minReadySeconds: 5
  progressDeadlineSeconds: 120
  selector:
    matchLabels:
      app.kubernetes.io/name: hello-app
  template:
    metadata:
      labels:
        app.kubernetes.io/name: hello-app
    spec:
      automountServiceAccountToken: false
      terminationGracePeriodSeconds: 30
      securityContext:
        runAsNonRoot: true
        runAsUser: 10001
        runAsGroup: 10001
        seccompProfile:
          type: RuntimeDefault
      containers:
        - name: hello-app
          image: your-registry.example/hello-docker:1.0
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 8080
          env:
            - name: APP_MESSAGE
              value: "Hello from Kubernetes!"
          resources:
            requests:
              cpu: 50m
              memory: 64Mi
            limits:
              cpu: 250m
              memory: 128Mi
          startupProbe:
            httpGet:
              path: /health
              port: http
            periodSeconds: 2
            failureThreshold: 30
          readinessProbe:
            httpGet:
              path: /health
              port: http
            periodSeconds: 5
            timeoutSeconds: 2
            failureThreshold: 2
          livenessProbe:
            httpGet:
              path: /health
              port: http
            periodSeconds: 10
            timeoutSeconds: 2
            failureThreshold: 3
          securityContext:
            allowPrivilegeEscalation: false
            readOnlyRootFilesystem: true
            capabilities:
              drop:
                - ALL
---
apiVersion: v1
kind: Service
metadata:
  name: hello-app
  namespace: docker-study
spec:
  selector:
    app.kubernetes.io/name: hello-app
  ports:
    - name: http
      port: 80
      targetPort: http
  type: ClusterIP
```

Important relationships in this manifest are:

- the Deployment's selector must match the Pod template labels;
- the Service selector must match the labels on the Pods;
- the Service's `targetPort: http` refers to the named container port;
- resource requests help scheduling, while limits constrain resource usage;
- the rollout allows one extra Pod but keeps existing replicas available while replacements become ready;
- the startup probe protects slow startup before liveness and readiness begin;
- readiness controls whether a Pod receives Service traffic;
- liveness detects an unrecoverable container failure that should trigger a restart;
- the security contexts require the image's non-root user, a read-only root filesystem, dropped capabilities, and the runtime's default seccomp profile;
- the Pod does not receive Kubernetes API credentials because this application does not need them.

The Docker example exposes one general `/health` endpoint. Real applications often separate liveness from readiness so that a temporary dependency failure stops traffic without causing a restart storm.

## Apply the Manifest

Confirm that `kubectl` is connected to the intended cluster:

```bash
kubectl config current-context
kubectl cluster-info
```

Validate before changing the cluster:

```bash
kubectl apply --dry-run=client -f kubernetes.yaml
kubectl apply --dry-run=server -f kubernetes.yaml
kubectl diff -f kubernetes.yaml
```

- Client dry-run does not persist resources and catches client-side generation and validation problems. `kubectl` may still contact a cluster for API discovery or schema information.
- Server dry-run also evaluates the cluster's API versions, admission controls, and defaults without persisting the objects.
- `diff` compares the live resources with the proposed state. It normally exits with status `1` when differences exist, which automation must distinguish from an execution error.

Review the active context and diff, then apply the desired state:

```bash
kubectl apply -f kubernetes.yaml
```

Wait for the Deployment to become available:

```bash
kubectl rollout status deployment/hello-app -n docker-study
```

List the common workload resources:

```bash
kubectl get all -n docker-study
kubectl get pods -n docker-study -o wide
```

## Access the Application

The example uses a `ClusterIP` Service, so it is not exposed publicly. Forward a local port to the Service for development and debugging:

```bash
kubectl port-forward service/hello-app 8080:80 -n docker-study
```

While that command is running, open `http://localhost:8080` or make a request from another terminal:

```bash
curl http://localhost:8080
```

In PowerShell:

```powershell
Invoke-RestMethod http://localhost:8080
```

Stop port forwarding with `Ctrl+C`. Port forwarding is a local debugging tool, not a production exposure strategy.

### External Traffic

Production exposure depends on the platform:

- a `LoadBalancer` Service requests a network load balancer where the environment supports one;
- Gateway API provides extensible routing and infrastructure roles;
- Ingress provides stable but frozen HTTP/HTTPS routing and requires an Ingress controller;
- TLS certificates, DNS, authentication, rate limiting, and source restrictions must be designed explicitly.

Do not expose every internal Service. Keep backends as `ClusterIP` unless clients outside the cluster genuinely need them.

## Interact with the Workload

### Inspect Resources

```bash
kubectl get deployment hello-app -n docker-study
kubectl get pods -n docker-study --show-labels
kubectl get service hello-app -n docker-study
kubectl describe deployment hello-app -n docker-study
kubectl describe pod POD_NAME -n docker-study
```

`get` shows a concise summary. `describe` adds configuration, current status, related conditions, and recent events. Replace `POD_NAME` with a name returned by `kubectl get pods`.

To view the stored manifest:

```bash
kubectl get deployment hello-app -n docker-study -o yaml
```

### Read Logs

```bash
kubectl logs POD_NAME -n docker-study
kubectl logs --follow POD_NAME -n docker-study
kubectl logs POD_NAME -n docker-study --previous
```

`--previous` is useful when a container has restarted and the earlier instance's logs are still available.

Read recent logs from all Pods matching the application label:

```bash
kubectl logs -n docker-study -l app.kubernetes.io/name=hello-app --all-containers=true --tail=100
```

### Execute a Command in a Container

```bash
kubectl exec POD_NAME -n docker-study -- whoami
kubectl exec POD_NAME -n docker-study -- ls -la /app
kubectl exec -it POD_NAME -n docker-study -- sh
```

The `--` separates `kubectl` arguments from the command passed to the container. Minimal images may have `sh` but not `bash`, and highly restricted images may contain no shell.

### View Events and Resource Usage

```bash
kubectl get events -n docker-study --sort-by=.metadata.creationTimestamp
kubectl top pods -n docker-study
kubectl top nodes
```

The `top` commands require the cluster's metrics API, commonly provided by Metrics Server.

## Scale the Application

Change the Deployment to three replicas immediately:

```bash
kubectl scale deployment hello-app --replicas=3 -n docker-study
kubectl get pods -n docker-study --watch
```

Press `Ctrl+C` to stop watching. For a durable declarative change, also update `replicas` in `kubernetes.yaml`; otherwise a later `kubectl apply` may restore the value in the file.

### Horizontal Pod Autoscaling

A HorizontalPodAutoscaler (HPA) changes a workload's replica count from observed metrics. A CPU-utilisation target depends on CPU requests being set:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: hello-app
  namespace: docker-study
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: hello-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

Resource metrics require a metrics API, commonly Metrics Server. Production scaling should be based on a metric that represents demand and should define stabilisation behaviour, capacity limits, and load-test evidence.

Avoid having both Git reconciliation and the HPA continually overwrite `spec.replicas`. Establish one owner; manifests managed with an HPA commonly omit the replica field after the initial handover.

### Availability and Disruption

Two replicas do not guarantee high availability: the scheduler may place them on the same node or failure domain. Consider topology-spread constraints or anti-affinity, sufficient spare cluster capacity, and multiple zones where the requirement justifies them.

A PodDisruptionBudget limits simultaneous voluntary evictions that use the eviction API:

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: hello-app
  namespace: docker-study
spec:
  minAvailable: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: hello-app
```

A PDB does not prevent node failure, direct Pod or Deployment deletion, or every form of disruption. It also does not control a Deployment's rolling-update strategy. An overly strict budget can prevent node maintenance.

### Graceful Termination

When a Pod terminates, Kubernetes marks its endpoint as terminating, runs any `preStop` hook, sends the container's stop signal, and waits up to `terminationGracePeriodSeconds` before forcefully stopping it.

The application should stop accepting new work, finish or safely abandon in-flight work, release leases, and exit within the grace period. A `preStop` hook is useful only for work the application cannot perform after receiving the signal; the hook consumes time from the same grace period.

## Release a New Image

Build and push a new immutable tag, such as `1.1`, then update the Deployment:

```bash
kubectl set image deployment/hello-app hello-app=your-registry.example/hello-docker:1.1 -n docker-study
kubectl rollout status deployment/hello-app -n docker-study
kubectl rollout history deployment/hello-app -n docker-study
```

If the release is unhealthy, roll it back:

```bash
kubectl rollout undo deployment/hello-app -n docker-study
```

Deployment rollback restores an earlier Pod template revision. It does not automatically revert a ConfigMap, Secret, Service, database migration, or external dependency. Design releases so application, configuration, and data changes remain compatible with the rollback plan.

Prefer unique, immutable image tags for releases. Reusing a tag makes it difficult to determine exactly which image is running and can produce inconsistent pulls.

For source-controlled environments, update the image tag in the manifest and run `kubectl apply` rather than allowing the live command and checked-in configuration to diverge.

## Configuration

Move non-sensitive values into a ConfigMap:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-app
  namespace: docker-study
data:
  APP_MESSAGE: "Configured with a ConfigMap"
```

Reference it from the Deployment container:

```yaml
env:
  - name: APP_MESSAGE
    valueFrom:
      configMapKeyRef:
        name: hello-app
        key: APP_MESSAGE
```

Environment variables are read when a container starts. After changing a referenced ConfigMap, restart the Deployment if the application does not reload configuration itself:

```bash
kubectl rollout restart deployment/hello-app -n docker-study
```

Create a Secret from an appropriately protected local file so the value is not written directly in shell history:

```bash
kubectl create secret generic hello-app-secret \
  --from-file=API_TOKEN=./api-token.txt \
  -n docker-study
```

Keep `api-token.txt` outside source control, restrict its permissions, and remove it securely according to the operating system and organisation's secret-handling process.

Reference the value without embedding it in the Pod specification:

```yaml
env:
  - name: API_TOKEN
    valueFrom:
      secretKeyRef:
        name: hello-app-secret
        key: API_TOKEN
```

Do not commit real secret values or base64-encoded Secret manifests to source control. Base64 is an encoding, not encryption. In production:

- encrypt Secret data at rest in the cluster;
- grant `get`, `list`, and `watch` access only where required;
- expose each Secret only to the containers that need it;
- avoid logging or copying values after the application reads them;
- use an approved external secret-management and rotation process where appropriate.

Secret values exposed as environment variables are fixed for the life of the container. Mounted Secret volumes can be updated, but the application must reload the file safely. Immutable Secrets require replacement rather than update.

## Managing Manifests

Keep the desired state reviewable and reproducible. Avoid making routine production changes only with imperative commands.

Common approaches include:

- plain YAML for a small number of resources;
- Kustomize bases and overlays, supported by `kubectl apply -k`;
- Helm charts for packaged, parameterised applications;
- an operator for domain-specific lifecycle automation;
- GitOps reconciliation when Git is the approved source of desired state.

Render and inspect generated YAML before applying it. Keep environment differences explicit, avoid copying whole manifests for one changed value, and test custom admission or policy requirements against the target cluster.

An owner should be clear for every field. An HPA, GitOps controller, manual command, and deployment pipeline should not compete to set the same value.

## Health Probes

Kubernetes supports three kinds of probes:

| Probe | Meaning |
| :--- | :--- |
| Readiness | Can this container receive traffic now? A failure removes the Pod from Service endpoints. |
| Liveness | Is this container stuck or unhealthy? Repeated failure causes a restart. |
| Startup | Has a slow-starting application finished initialising? It delays liveness and readiness checks until startup succeeds. |

A probe should test the condition it represents. A liveness endpoint should not usually depend on an external database, because a database outage could cause every application container to restart without fixing the dependency.

## Resource Requests and Limits

- A **request** is the amount the scheduler uses when deciding where a Pod can fit.
- A **limit** is the maximum enforced usage for that container.
- CPU limits throttle usage when exceeded.
- Exceeding a memory limit can cause the container to be terminated for running out of memory.

Set realistic values from observed application behaviour. Requests that are too high waste capacity; values that are too low can cause contention, throttling, or unstable workloads.

## Persistent Data

The writable filesystem of a container is ephemeral. Kubernetes storage concepts include:

- **Volume**: storage made available to containers in a Pod;
- **PersistentVolume**: a cluster storage resource;
- **PersistentVolumeClaim**: a workload's request for persistent storage;
- **StorageClass**: describes a class of storage and often enables dynamic provisioning.

Deployments suit stateless replicas. Stateful applications may require persistent volumes, stable identities, ordered behaviour, and a StatefulSet.

## Labels, Selectors, and Annotations

- **Labels** are identifying key-value pairs used to group and select resources.
- **Selectors** connect objects, such as a Service to its Pods.
- **Annotations** store non-identifying metadata used by tools and integrations.

A Service with an incorrect selector may exist normally but have no endpoints. Compare its selector with the Pod labels when debugging connectivity.

```bash
kubectl get service hello-app -n docker-study -o jsonpath='{.spec.selector}'
kubectl get pods -n docker-study --show-labels
kubectl get endpointslices -n docker-study -l kubernetes.io/service-name=hello-app
```

## Security Boundaries

Kubernetes security is layered. A Namespace organises namespaced resources, but it does not by itself isolate networks, API permissions, nodes, or kernel access.

### Pod Security

The example's security context follows the main Restricted Pod Security Standard expectations for a Linux workload. A cluster administrator can enforce Pod Security Standards with namespace labels and should pin an enforcement version that matches the cluster's upgrade policy.

Important controls include:

- run as a non-root user;
- prevent privilege escalation;
- drop unnecessary Linux capabilities;
- use `RuntimeDefault` or a stricter seccomp profile;
- avoid privileged mode, host namespaces, and unsafe host paths;
- use a read-only root filesystem where the application supports it.

Security context fields configure a workload, while admission policy ensures that unsafe workloads cannot bypass the organisation's requirements.

### Service Accounts and RBAC

Every Pod has a service account identity. Disable automatic token mounting when the workload does not call the Kubernetes API, as the example does.

When API access is required:

- create a dedicated service account;
- grant only the required verbs on specific resource types and names where practical;
- prefer a namespaced `Role` and `RoleBinding` over cluster-wide access;
- avoid wildcard resources and verbs;
- review who can create Pods, because Pod creation can provide paths to Secrets and node resources.

Check an identity's effective permission:

```bash
kubectl auth can-i list pods -n docker-study
kubectl auth can-i get secrets -n docker-study
kubectl auth can-i --list -n docker-study
```

### NetworkPolicy

Pods can generally communicate unless network policy restricts them. A NetworkPolicy selects Pods and allows defined ingress or egress traffic:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: hello-app-ingress
  namespace: docker-study
spec:
  podSelector:
    matchLabels:
      app.kubernetes.io/name: hello-app
  policyTypes:
    - Ingress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              kubernetes.io/metadata.name: docker-study
      ports:
        - protocol: TCP
          port: 8080
```

This policy isolates the selected Pods for ingress and allows port `8080` from Pods in the same Namespace. NetworkPolicy rules are additive, so another policy may allow additional traffic. It has an effect only when the cluster's network implementation enforces NetworkPolicy. Define egress and DNS access deliberately before introducing default-deny policies.

## Observability and Debugging

Kubernetes exposes resource state, events, and container logs, but it does not automatically provide durable application logging, metrics, traces, dashboards, or alerts.

A production workload should expose useful application telemetry and preserve it outside disposable Pods. Correlate application signals with Deployment revisions, Pod identity, node, zone, and image digest.

When a minimal image has no shell or diagnostic tools, do not add them permanently only for emergencies. Where cluster policy permits, `kubectl debug` can add an ephemeral debugging container or create a diagnostic copy of a Pod:

```bash
kubectl debug -it POD_NAME -n docker-study --image=busybox:1.37 --target=hello-app
```

Debug containers are privileged operational access. Apply RBAC, image policy, audit logging, and data-handling controls accordingly.

## Common Problems

### Pod Is Pending

The scheduler may be unable to find a node with sufficient resources, satisfy placement rules, or mount required storage:

```bash
kubectl describe pod POD_NAME -n docker-study
kubectl get events -n docker-study --sort-by=.metadata.creationTimestamp
```

### ImagePullBackOff or ErrImagePull

Check the image name and tag, registry access, pull credentials, and whether the image exists for the node's architecture:

```bash
kubectl describe pod POD_NAME -n docker-study
```

### CrashLoopBackOff

The container repeatedly starts and exits or fails a health check. Inspect its current and previous logs, exit reason, command, configuration, and probes:

```bash
kubectl logs POD_NAME -n docker-study
kubectl logs POD_NAME -n docker-study --previous
kubectl describe pod POD_NAME -n docker-study
```

### Service Has No Reachable Application

Check that the Service selector matches Pod labels, the Pods are ready, `targetPort` matches the application port, and the application listens on `0.0.0.0`:

```bash
kubectl get pods,service,endpointslices -n docker-study
kubectl describe service hello-app -n docker-study
```

### Rollout Does Not Complete

A Deployment can stall when new Pods cannot schedule, pull the image, start, or become ready:

```bash
kubectl rollout status deployment/hello-app -n docker-study
kubectl describe deployment hello-app -n docker-study
kubectl get replicasets,pods -n docker-study
kubectl get events -n docker-study --sort-by=.metadata.creationTimestamp
```

Do not increase `progressDeadlineSeconds` until the cause is understood. Check requests, quotas, image access, probes, security policy, and application startup.

### Container Is OOMKilled or CPU Throttled

Inspect the termination reason, limits, and observed metrics:

```bash
kubectl describe pod POD_NAME -n docker-study
kubectl top pod POD_NAME -n docker-study --containers
```

An out-of-memory kill requires investigation of workload demand, leaks, heap configuration, and an appropriate memory limit. CPU limits can introduce throttling and latency; change resources from measurement rather than simply raising every limit.

### DNS or Dependency Lookup Fails

Use the Service DNS name rather than a changing Pod IP. Within the same Namespace, `hello-app` resolves the Service; across Namespaces use `hello-app.docker-study` or its fully qualified cluster name.

Check the Service, EndpointSlices, network policy, and cluster DNS health. A Service can resolve correctly while having no ready endpoints.

### Permission Is Denied

Kubernetes uses role-based access control in most clusters. Check whether the current identity may perform an action:

```bash
kubectl auth can-i get pods -n docker-study
kubectl auth can-i create deployments -n docker-study
```

## Clean Up

The example resources all live in their own Namespace, so removing it removes the namespaced resources inside it:

```bash
kubectl delete namespace docker-study
```

This is destructive for everything in that Namespace. If the Namespace contains anything else you need, delete only this example's manifest instead:

```bash
kubectl delete -f kubernetes.yaml
```

Namespace deletion can also delete PersistentVolumeClaims. Whether backing storage is retained or deleted depends on the PersistentVolume and StorageClass reclaim policy. Confirm data-retention behaviour before deleting a stateful environment.

## Quick Command Reference

| Task | Command |
| :--- | :--- |
| Check the active cluster | `kubectl config current-context` |
| Validate locally | `kubectl apply --dry-run=client -f kubernetes.yaml` |
| Validate with the API server | `kubectl apply --dry-run=server -f kubernetes.yaml` |
| Preview changes | `kubectl diff -f kubernetes.yaml` |
| Apply a manifest | `kubectl apply -f kubernetes.yaml` |
| List common workload resources | `kubectl get all -n docker-study` |
| Inspect a Pod | `kubectl describe pod POD_NAME -n docker-study` |
| Read logs | `kubectl logs POD_NAME -n docker-study` |
| Open a shell | `kubectl exec -it POD_NAME -n docker-study -- sh` |
| Forward a local port | `kubectl port-forward service/hello-app 8080:80 -n docker-study` |
| Scale a Deployment | `kubectl scale deployment hello-app --replicas=3 -n docker-study` |
| Watch a rollout | `kubectl rollout status deployment/hello-app -n docker-study` |
| Roll back | `kubectl rollout undo deployment/hello-app -n docker-study` |
| Delete the example | `kubectl delete -f kubernetes.yaml` |

Despite its name, `kubectl get all` does not return every resource type. Query important resources such as ConfigMaps, Secrets, NetworkPolicies, Ingresses or Gateways, PersistentVolumeClaims, and RBAC objects explicitly.

## Review Checklist

- [ ] The active context and Namespace are confirmed before every change.
- [ ] Manifests pass local, server-side, policy, and rendered-output validation.
- [ ] Images use intentional immutable tags or digests and are available for every node architecture.
- [ ] Requests and limits come from observed behaviour.
- [ ] Startup, readiness, and liveness probes represent different failure decisions.
- [ ] Rolling-update and graceful-termination settings protect in-flight work.
- [ ] Replicas are spread across the failure domains required for availability.
- [ ] HPA, GitOps, and deployment tooling have non-conflicting field ownership.
- [ ] Pods run with least privilege and do not receive unnecessary API tokens.
- [ ] RBAC, Secrets, and network access follow least privilege.
- [ ] Persistent-data retention and backup behaviour are understood.
- [ ] Logs, metrics, traces, events, and rollout metadata support diagnosis.
- [ ] API deprecations and cluster/client version compatibility are checked before upgrades.
- [ ] Rollback includes compatible configuration and data changes, not only an older image.

## Official Documentation

- [Kubernetes concepts](https://kubernetes.io/docs/concepts/)
- [Kubernetes components](https://kubernetes.io/docs/concepts/overview/components/)
- [`kubectl` overview](https://kubernetes.io/docs/concepts/overview/kubectl/)
- [Run applications](https://kubernetes.io/docs/tasks/run-application/)
- [Access applications in a cluster](https://kubernetes.io/docs/tasks/access-application-cluster/)
- [Configuration](https://kubernetes.io/docs/concepts/configuration/)
- [Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/)
- [Network policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
- [Horizontal Pod Autoscaling](https://kubernetes.io/docs/concepts/workloads/autoscaling/horizontal-pod-autoscale/)
- [Disruptions](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)

## Related Guides

- [Docker](./docker.md)
- [Technology Stack](../engineering-foundations/technology-stack.md)
- [Testing](../quality-engineering/testing.md)
