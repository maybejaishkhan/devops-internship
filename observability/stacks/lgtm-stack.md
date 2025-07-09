# LGTM Stack

Here's the completed table with the correct tools, their purposes, and default ports:

| Tool                    | Purpose                                    | Default Port                                    |
| ----------------------- | ------------------------------------------ | ----------------------------------------------- |
| Grafana                 | Dashboards                                 | 3000                                            |
| Prometheus              | Metrics collection & querying              | 9090                                            |
| Loki                    | Log aggregation                            | 3100                                            |
| Tempo                   | Distributed tracing backend                | 3200                                            |
| Alloy                   | Telemetry pipeline (logs, metrics, traces) | *None (depends on config)*                      |
| OpenTelemetry Collector | Receives and exports telemetry data        | 4317 (gRPC), 4318 (HTTP), 8888 (metrics scrape) |

**Exporters** for Prometheus

- `prom/node-exporter` at `:3100` - For getting linux system metrics.
- `gcr.io/cadvisor/cadvisor` at `:8080` - For getting docker container metrics.

## Setup

### Project Setup

First, create a project folder `mkdir observability && cd observability`.

- Create folders `mkdir -p grafana/provisioning/{datasources,dashboards}`.
  - Create grafana datasources and dashboards files (optional).
- Create config files for each tool (optionally you can also create individual folders). We can name them whatever but just have to be careful when mounting them.
- Create .env for docker compose.

<details>
  <summary><b>Grafana datasources and dashboards yml files</b></summary>

  `datasources.yml` - Tells grafana what datasources to load at startup and how.

  ```yml
  apiVersion: 1

  datasources:
    - name: Prometheus
      type: prometheus
      access: proxy
      url: http://prometheus:9090
      isDefault: true

    - name: Loki
      type: loki
      access: proxy
      url: http://loki:3100

    - name: Tempo
      type: tempo
      access: proxy
      url: http://tempo:3200
  ```

  `dashboards.yml` - Tells grafana where to find dashboard JSON files on disk (to load at startup) and where to place them inside of the Grafana UI.

  ```yml
  apiVersion: 1

  providers:
    - name: 'default'                # Internal name for this provider
      folder: ''                     # Folder name in Grafana UI ('' = General)
      type: file                     # Load dashboards from files
      options:
        path: /etc/grafana/provisioning/dashboards  # Path inside container
        recurse: false

  ```

</details>

<details>
  <summary><b>Prometheus config - <code>prometheus-config.yml</code></b></summary>

  ```yml
  global:
    scrape_interval: 10s

  scrape_configs:
    - job_name: 'prometheus'
      static_configs:
        - targets: ['prometheus:9090']
    - job_name: 'otel-collector'
      static_configs:
        - targets: ['otel-collector:8888']
  ```

</details>

<details>
  <summary><b>Loki config - <code>loki-config.yml</code></b></summary>

  Create loki config file `nano loki-config.yml`

  1. Create folders `mkdir -p grafana/provisioning/{datasources,dashboards}`
  2. (Optional) Create datasource file `datasources.yml`

  ```bash

  ```

</details>

<details>
  <summary><b>Tempo config - <code>tempo-config.yml</code></b></summary>

  1. Create folders `mkdir -p grafana/provisioning/{datasources,dashboards}`
  2. (Optional) Create datasource file `datasources.yml`

  ```bash
  echo "Code block"
  ```

</details>

<details>
  <summary><b>Alloy config - <code>alloy-config.alloy</code></b></summary>
  ```alloy
  ```
</details>

<details>
  <summary><b>OpenTelemetry - <code>otel-config.yml</code></b></summary>

  1. Create folders `mkdir -p grafana/provisioning/{datasources,dashboards}`
  2. (Optional) Create datasource file `datasources.yml`

  ```bash
  echo "Code block"
  ```

</details>

<details>
  <summary><b>Docker Compose <code>.env</code></b></summary>

  ```env
  echo "Code block"
  ```

</details>
