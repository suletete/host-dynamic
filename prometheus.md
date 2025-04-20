---

# 📡 Monitor Linux Server using Prometheus Node Exporter

## 📘 Introduction

Monitoring is vital for maintaining the health and performance of Linux servers. **Prometheus Node Exporter** is a lightweight, powerful exporter that gathers hardware and OS-level metrics. When integrated with **Prometheus**, it enables real-time visibility into the server’s state.

This project will walk you through installing and configuring Node Exporter, integrating it with Prometheus, and exploring key metrics.

---

## 🎯 Objectives

- Install and configure Prometheus Node Exporter on a Linux server.
- Integrate Node Exporter with a Prometheus server to collect metrics.
- Explore system-level metrics such as CPU, memory, disk, and network.
- Set up basic metric queries using the Prometheus UI.
- *(Optional)* Configure alert rules for critical server conditions.

---

## 🧾 Prerequisites

- 🖥️ A Linux server with `sudo` privileges.
- 📦 A running Prometheus instance (local or remote).
- 🌐 Network access between Prometheus and the server on port **9100**.
- 🧰 Tools: Terminal access, a text editor, and access to the Prometheus web UI.

⏱️ **Estimated Time**: 1–2 hours

---

## 📋 Tasks Outline

1. Install Prometheus Node Exporter.
2. Start and enable Node Exporter as a system service.
3. Configure Prometheus to scrape metrics from Node Exporter.
4. Verify and query metrics in Prometheus.
5. Analyze and explore system metrics.

---

## 🚀 Project Tasks

---

### ✅ Task 1: Install Prometheus Node Exporter

1. Download the latest Node Exporter binary:

   ```bash
   curl -LO https://github.com/prometheus/node_exporter/releases/latest/download/node_exporter-linux-amd64.tar.gz
   ```

2. Extract the tarball:

   ```bash
   tar -xvf node_exporter-linux-amd64.tar.gz
   ```

3. Move the binary to your system PATH:

   ```bash
   sudo mv node_exporter-linux-amd64/node_exporter /usr/local/bin/
   ```

---

### ⚙️ Task 2: Start and Enable Node Exporter as a Service

1. Create a systemd service file:

   ```bash
   sudo nano /etc/systemd/system/node_exporter.service
   ```

2. Paste the following content:

   ```ini
   [Unit]
   Description=Prometheus Node Exporter
   After=network.target

   [Service]
   User=nobody
   ExecStart=/usr/local/bin/node_exporter
   Restart=always

   [Install]
   WantedBy=multi-user.target
   ```

3. Reload systemd and start the service:

   ```bash
   sudo systemctl daemon-reload
   sudo systemctl start node_exporter
   sudo systemctl enable node_exporter
   ```

4. Confirm it's running:

   ```bash
   sudo systemctl status node_exporter
   ```

5. Open your browser and visit:

   ```
   http://<your-server-ip>:9100/metrics
   ```

   You should see raw metrics being exposed.

---

### 🔧 Task 3: Configure Prometheus to Scrape Node Exporter

1. Open the Prometheus configuration file:

   ```bash
   sudo nano /etc/prometheus/prometheus.yml
   ```

2. Add a scrape job under `scrape_configs`:

   ```yaml
   scrape_configs:
     - job_name: 'node-exporter'
       static_configs:
         - targets: ['<your-server-ip>:9100']
   ```

3. Restart Prometheus to apply the changes:

   ```bash
   sudo systemctl restart prometheus
   ```

---

### 🔍 Task 4: Verify and Query Node Exporter Metrics

1. Visit Prometheus UI:

   ```
   http://<prometheus-server-ip>:9090
   ```

2. Run queries to test metrics:

   Example query:

   ```promql
   node_cpu_seconds_total
   ```

3. Go to **Status > Targets** and check if Node Exporter is listed and marked as **UP**.

---

### 📊 Task 5: Explore and Analyze Metrics

Use the Prometheus query interface to explore important metrics:

| Metric | Description |
|--------|-------------|
| `node_memory_MemAvailable_bytes` | Available memory |
| `node_filesystem_avail_bytes` | Available disk space |
| `node_network_receive_bytes_total` | Network bytes received |
| `rate(node_cpu_seconds_total[5m])` | CPU usage rate over the last 5 minutes |

You can visualize these in time series or graph mode in Prometheus.

---

