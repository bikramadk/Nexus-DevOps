# Nexus DevOps — Todo Notes App

A simple Todo Notes web app built as a hands-on DevOps project.

The application itself is straightforward — create, update, and delete notes with priority levels. The main focus of this project is implementing a **production-style CI/CD pipeline on Azure**, including automated deployments and monitoring.

---

## What's Inside

* **App**

  * Node.js + Express backend
  * Vanilla HTML/CSS/JS frontend
  * MongoDB database

* **CI/CD**

  * Jenkins pipeline
  * Auto-deploy on every GitHub push

* **Infrastructure**

  * Dockerized application
  * Hosted on Azure VM

* **Monitoring**

  * Prometheus for metrics scraping
  * Grafana for visualization

---

##  Prerequisites

Before starting, ensure you have:

* Azure account with at least one VM (Ubuntu 22.04 recommended)
* Git installed locally
* Docker installed locally (for testing)
* GitHub account

---

## Step 1 — Clone the Repository

```bash
git clone https://github.com/bikramadk/Nexus-DevOps.git
cd Nexus-DevOps
```

---

## Step 2 — Run the App Locally with Docker

No need to install MongoDB locally — Docker Compose handles everything.

```bash
docker-compose up --build
```

Open your browser at:
http://localhost:3000

To stop the app:

```bash
docker-compose down
```

---

##  Step 3 — Set Up Jenkins on Azure VM

SSH into your Azure VM and install Jenkins:


Access Jenkins at:
http://<your-vm-ip>:8080

Complete setup and install suggested plugins.

---

##  Step 4 — Install Docker on Azure VM

---

##  Step 5 — Create Jenkins Pipeline

1. Open Jenkins → New Item
2. Name: `todo-app-pipeline`
3. Select: **Pipeline**

### Configure:

* Definition: 
* SCM: Git
* Repo URL:

  ```
  https://github.com/bikramadk/Nexus-DevOps.git
  ```
* Branch: `*/main`
* Script Path: `Jenkinsfile`

Click **Save → Build Now**

### Pipeline Stages:

1. Clone repository
2. Build Docker image
3. Stop old containers
4. Start new containers

---

##  Step 6 — Set Up GitHub Webhook (Auto Deploy)

### Jenkins:

* Go to pipeline → Configure
* Enable: `GitHub hook trigger for GITScm polling`

### GitHub:

* Repo → Settings → Webhooks → Add webhook

```
Payload URL: http://<your-vm-ip>:8080/github-webhook/
Content type: application/json
Event: Just the push event
```

Now every push triggers automatic deployment.

---

##  Step 7 — Set Up Monitoring with Prometheus

### Install Node Exporter:

### Create Service:

### Start Service:

```bash
sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl enable node_exporter
```

---

## Step 8 — Configure Prometheus

Edit config:

```bash
nano /tmp/prometheus.yml
```

Add:

```yaml
- job_name: 'todo-app'
  static_configs:
    - targets: ['<your-app-vm-ip>:9091']

- job_name: 'todo-node-exporter'
  static_configs:
    - targets: ['<your-app-vm-ip>:9100']
```

Restart:

```bash
docker restart prometheus
```

Check targets:
http://<monitoring-vm-ip>:9090/targets

---

##  Step 9 — Connect Grafana

Open:
http://<monitoring-vm-ip>:3000

### Steps:

1. Connections → Data Sources
2. Add **Prometheus**
3. URL:

   ```
   http://<monitoring-vm-ip>:9090
   ```
4. Save & Test

### Suggested Metrics:

* CPU Usage → `process_cpu_user_seconds_total`
* Memory → `process_resident_memory_bytes`
* Event Loop → `nodejs_eventloop_lag_seconds`
* Uptime → `up{job="todo-app"}`

---

## 🔌 Port Reference

| Port | Service       | Location      |
| ---- | ------------- | ------------- |
| 3000 | Todo App      | App VM        |
| 8080 | Jenkins       | App VM        |
| 9091 | App Metrics   | App VM        |
| 9100 | Node Exporter | App VM        |
| 9090 | Prometheus    | Monitoring VM |
| 3000 | Grafana       | Monitoring VM |

---

##  Full Pipeline Flow

```
git push → GitHub → Webhook → Jenkins → Docker build → Deploy → Live
                                                     ↓
                                              Prometheus
                                                     ↓
                                               Grafana
```

---

##  Author

**Bikram Raj Adhikari**
GitHub: https://github.com/bikramadk
