
---

# 🚀 Setup Prometheus Node Exporter on Kubernetes

## 📘 Introduction

**Prometheus** is a powerful open-source monitoring and alerting toolkit. The **Node Exporter** collects system-level metrics like CPU, memory, disk, and network stats. By deploying Node Exporter as a **DaemonSet** in a Kubernetes cluster, you ensure that each node in the cluster exposes its metrics for centralized monitoring via Prometheus.

---

## 🎯 Objectives

- Understand what Prometheus Node Exporter does.
- Deploy Node Exporter as a **DaemonSet** on Kubernetes.
- Configure Prometheus to **scrape** Node Exporter metrics.
- Visualize and analyze metrics using the Prometheus UI.
- Optionally, extend the setup with alerting and dashboards.

---

## 🧾 Prerequisites

- 🧱 A working **Kubernetes Cluster** (e.g., Minikube, Kind, EKS, AKS, GKE).
- 🔧 `kubectl` CLI configured to interact with your cluster.
- 📈 A Prometheus instance running inside the cluster.
- ✍️ A text editor to modify YAML configuration files.

⏱️ **Estimated Time**: 2–4 hours

---

## 🗂️ Tasks Outline

1. Understand how Node Exporter works.
2. Deploy Node Exporter as a DaemonSet.
3. Configure Prometheus to scrape metrics.
4. Verify metric collection via Prometheus UI.
5. Explore available system metrics.

---

## 🔨 Project Tasks

---

### ✅ Task 1: Understand How Node Exporter Works

**Node Exporter** runs on each Kubernetes node and exposes hardware and OS metrics over HTTP on port **9100**.

🔍 **Collected Metrics** include:

- CPU usage
- Memory usage
- Disk I/O
- Network statistics
- Filesystem usage

By running as a **DaemonSet**, one instance is deployed per node to ensure comprehensive coverage.

---

### ⚙️ Task 2: Deploy Node Exporter as a DaemonSet

1. Create a file named `node-exporter-daemonset.yaml`:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
  namespace: monitoring
spec:
  selector:
    matchLabels:
      app: node-exporter
  template:
    metadata:
      labels:
        app: node-exporter
    spec:
      containers:
        - name: node-exporter
          image: prom/node-exporter:latest
          ports:
            - containerPort: 9100
              name: metrics
          securityContext:
            runAsNonRoot: true
            allowPrivilegeEscalation: false
          resources:
            limits:
              memory: "100Mi"
              cpu: "100m"
            requests:
              memory: "50Mi"
              cpu: "50m"
```

2. Apply the manifest:

```bash
kubectl apply -f node-exporter-daemonset.yaml
```

3. Confirm successful deployment:

```bash
kubectl get daemonset -n monitoring
```

---

### 🧩 Task 3: Configure Prometheus to Scrape Node Exporter Metrics

1. Open your Prometheus config (usually a ConfigMap or file: `prometheus.yml`):

```yaml
scrape_configs:
  - job_name: 'node-exporter'
    kubernetes_sd_configs:
      - role: endpoints
    relabel_configs:
      - source_labels: [__meta_kubernetes_service_label_app]
        action: keep
        regex: node-exporter
```

> Ensure Node Exporter is exposed as a Kubernetes service with the `app=node-exporter` label.

2. Apply the config changes (or patch the ConfigMap and rollout Prometheus again).

3. Restart Prometheus to load the new configuration:

```bash
kubectl rollout restart deployment prometheus-server -n monitoring
```

---

### 🔎 Task 4: Verify Metrics in Prometheus

1. Port-forward Prometheus UI (if not externally accessible):

```bash
kubectl port-forward svc/prometheus 9090:9090 -n monitoring
```

2. Visit the Prometheus UI at:

```
http://localhost:9090
```

3. Run a query to confirm metrics:

```promql
node_cpu_seconds_total
```

4. Check **Status > Targets** to verify that all Node Exporter endpoints are marked **UP**.

---

### 📊 Task 5: Explore Node Exporter Metrics

Here are some useful Node Exporter metrics and queries:

| Metric | Description |
|--------|-------------|
| `node_memory_MemAvailable_bytes` | Available memory on a node |
| `node_filesystem_avail_bytes` | Free disk space |
| `node_network_receive_bytes_total` | Network traffic received |
| `rate(node_cpu_seconds_total[5m])` | CPU usage trend |

These metrics help you build insights into your cluster’s performance and can be visualized or used to trigger alerts.

---
