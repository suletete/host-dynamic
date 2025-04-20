
---

# 📡 Configuring Uptime Monitoring Using Gatus

## 📘 Introduction

Ensuring your applications, APIs, and websites are consistently available is key to reliability and user trust. **Gatus** is a lightweight, self-hosted monitoring solution that allows you to track the uptime and responsiveness of your services with minimal configuration.

---

## 🎯 Objectives

- Understand Gatus and its role in uptime monitoring.
- Deploy Gatus using Docker.
- Configure Gatus to monitor multiple HTTP endpoints.
- Set up alerting (e.g., via Slack) for outages.
- Use the Gatus dashboard for insights and visualization.

---

## 🧾 Prerequisites

- 🧠 Basic familiarity with HTTP, APIs, and YAML files.
- 🐳 Docker installed on your machine.
- ✍️ A text editor (e.g., VSCode, nano).
- 🌐 Internet access to test endpoints.

⏱️ **Estimated Time**: 1–2 hours

---

## 🗂️ Tasks Outline

1. Install and run Gatus locally using Docker.
2. Create a configuration file to define monitoring targets.
3. Add live endpoints and test monitoring behavior.
4. Set up alerting via Slack or other providers.
5. Customize and explore the Gatus dashboard.

---

## 🔧 Project Tasks

---

### ✅ Task 1: Install and Run Gatus Locally

1. **Install Docker** (if not already installed):

   - Follow the official guide: https://docs.docker.com/get-docker/

2. **Pull the Gatus Docker image**:

   ```bash
   docker pull twinproduction/gatus
   ```

3. **Create a configuration directory**:

   ```bash
   mkdir gatus && cd gatus
   mkdir config
   ```

4. **Run Gatus with volume mount for configuration**:

   ```bash
   docker run -d \
     -p 8080:8080 \
     --name gatus \
     -v $(pwd)/config:/config \
     twinproduction/gatus
   ```

5. **Access the Gatus UI**:

   - Open your browser and go to: [http://localhost:8080](http://localhost:8080)

---

### 📝 Task 2: Create a Basic Configuration File

1. Create the `config.yaml` file in the `gatus/config` directory:

   ```yaml
   endpoints:
     - name: Example Website
       url: "https://example.com"
       interval: 60s
       conditions:
         - "[STATUS] == 200"
   ```

2. Restart the Gatus container to apply the new config:

   ```bash
   docker restart gatus
   ```

---

### 🌐 Task 3: Add and Test Monitoring Targets

1. Update `config.yaml` to include more endpoints:

   ```yaml
   endpoints:
     - name: GitHub
       url: "https://github.com"
       interval: 60s
       conditions:
         - "[STATUS] == 200"

     - name: Nonexistent
       url: "https://thiswebsitedoesnotexist.com"
       interval: 60s
       conditions:
         - "[STATUS] == 200"
   ```

2. Restart Gatus:

   ```bash
   docker restart gatus
   ```

3. Check the dashboard — the **Nonexistent** service should display a failure status.

---

### 🔔 Task 4: Configure Alerts for Downtime

#### Example: Slack Alerting

1. **Generate a Slack webhook URL** from your workspace.

2. Add the following to your `config.yaml`:

   ```yaml
   alerts:
     - type: slack
       url: "https://hooks.slack.com/services/YOUR/WEBHOOK/URL"
       failure-threshold: 2
       success-threshold: 2
   ```

3. Simulate an outage (e.g., use an invalid URL) and wait for two failed checks to receive a Slack alert.

> Gatus supports other alerting methods like Discord, SMTP (email), PagerDuty, etc.

---

### 📊 Task 5: Explore and Customize Gatus Dashboard

- Access the dashboard at [http://localhost:8080](http://localhost:8080).
- Customize config with dashboard themes, logos, and descriptions.
- Adjust `interval` values for more or less frequent checks.
- Use advanced conditions like:
  ```yaml
  conditions:
    - "[STATUS] == 200"
    - "[BODY] CONTAINS 'Welcome'"
    - "[RESPONSE_TIME] < 500"
  ```
