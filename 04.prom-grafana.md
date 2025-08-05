# 📊 Monitoring OpenSearch Using Prometheus & Grafana

## ✅ What’s Covered

- Setup of Prometheus Exporter Plugin for OpenSearch
- Docker-based deployment of Prometheus and Grafana
- Configuration of Prometheus to scrape OpenSearch metrics
- Connecting Prometheus as a datasource to Grafana
- Live visualization of OpenSearch performance metrics
- Troubleshooting and networking challenges resolved

---
Demo link : https://drive.google.com/file/d/1om6Ps7ZWgvhNQV2K6Qn0habvUfbElaRf/view?usp=sharing

---

## 📁 Project Structure

```

open-search.
├── docker-compose.yml
├── prometheus.yml
└── opensearch-exporter
       |
       |___config/opensearch.yml
       |
       |___Dockerfile
```

---

## 🚀 Steps Followed

### 1. 📥 Download Prometheus Exporter Plugin

Used official plugin:

```

[https://github.com/aiven/prometheus-exporter-plugin-for-opensearch/releases/download/2.14.0.0/prometheus-exporter-2.14.0.0.zip](https://github.com/aiven/prometheus-exporter-plugin-for-opensearch/releases/download/2.14.0.0/prometheus-exporter-2.14.0.0.zip)

````

Placed the ZIP under `./plugins` to be mounted into the OpenSearch container.

---

### 2. 🐳 Docker Compose File

Here’s how the services were defined:

#### ✅ Services:

- **OpenSearch** with the Prometheus Exporter plugin pre-installed
- **Prometheus** configured to scrape metrics from OpenSearch
- **Grafana** for visualization

#### 🚨 Port Mapping:

| Service     | Internal | Host          |
|-------------|----------|---------------|
| OpenSearch  | 9200     | 9200          |
| Prometheus  | 9090     | 9090          |
| Grafana     | 3000     | 3000          |

---

### 3. ⚙️ prometheus.yml Configuration

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'opensearch'
    static_configs:
      - targets: ['host.docker.internal:9600']
````

* `9600` is the default port where the OpenSearch Prometheus Exporter exposes metrics.
* Used `host.docker.internal` to resolve the host IP inside Docker on Windows.

---

## 🧪 Testing the Setup

### ✅ OpenSearch:

* Exposed on [http://localhost:9200](http://localhost:9200)

### ✅ Prometheus:

* UI: [http://localhost:9090](http://localhost:9090)
* Successfully queried `http://host.docker.internal:9600/metrics`
* Confirmed data is visible from target

### ✅ Grafana:

* UI: [http://localhost:3000](http://localhost:3000)

* Default creds: `admin/admin`

* Added Prometheus as a Data Source using:

  ```
  URL: http://host.docker.internal:9090
  ```

* Query tested: `up` — confirmed connection

---

## 🐞 Errors Faced & Fixes

| ❌ Issue                                                                            | ✅ Solution                                                                                                                         |
| ---------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Prometheus could not connect to OpenSearch Exporter: `connect: connection refused` | Used `host.docker.internal` instead of `localhost` to ensure cross-container visibility (especially on Docker Desktop for Windows) |
| Grafana showed no data sources                                                     | Restarted Grafana after Prometheus was up                                                                                          |
| Port `9600` not exposed                                                            | Explicitly exposed in Docker Compose                                                                                               |
| Prometheus endpoint showed `connection refused`                                    | Made sure container was running and `prometheus.yml` had correct target                                                            |

---

## 📊 Visualization Example

Once the data source was added, explored metrics like:

* `opensearch_breakers_parent_estimated_size_bytes`
* `opensearch_cluster_health_status`
* `opensearch_indices_docs_count`

---

## 🧠 Key Learnings

* Plugin integration is vital for exporting OpenSearch metrics.
* `host.docker.internal` is a crucial trick for container-to-host communication on Windows.
* Prometheus and Grafana can be containerized for quick observability setup.
* Real-time monitoring of OpenSearch is now live and visual.

---


