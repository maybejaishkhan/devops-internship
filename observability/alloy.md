# Grafana Alloy

An open-source modern tool to collect telemetry (metrics, logs, traces), in a unified way by Grafana Labs. It can replace OpenTelemetry Collectors, Promtail (deprecated) and Exporters (node, cadvisor etc).

This simplifies the general architecture much more (although alloy itself is very complicated).

![Alloy Flow](alloy-flow.png)

## Configuration

Alloy uses `config.alloy` file for its configuration. This can be split into multiple files (and linked via `import`).

We define **Alloy Components** in the config file. These components form component blocks. There are 3 types of components:

1. Sources (scrape, collect, receive)
2. Processors (filter, relabel, enrich)
3. Exporters/Writers (send to remote systems)

## Grafana Alloy Components

- **Infrastructure:** `prometheus.*` (metrics), `loki.*` (logs), `otelcol.reciever.*` (traces), `pyroscope.*` (profiles) components. 
- **Applications:** `otelcol.reciever.*` (metrics, logs, traces) and `pyroscope.*` (profiles) components.

### Docker Containers Metrics and Logs

### Linux System Metrics and Logs

### Local Log Files

### OTel-Instrumented Traces

### Pyroscope Profiles