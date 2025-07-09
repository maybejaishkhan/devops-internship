# Observability

The ability to understand the internal state of a system from its external signals.

> **“Can I understand what’s happening inside my system just by looking at its outputs?”**

1. **Telemetry** is the actual data collected from the systems.
2. **Monitoring** is watching specific signals over time and alerting when something breaks or crosses a threshold.
3. **Alerting** is notifying the output.

---

- [The Three Pillars of Observability](#the-three-pillars-of-observability)
- [Monitoring Types \& Methods](#monitoring-types--methods)
- [DevOps Observability Tools](#devops-observability-tools)
- [Advanced Concepts](#advanced-concepts)
  - [Alerting Strategies](#alerting-strategies)
  - [Correlation \& Enrichment](#correlation--enrichment)
  - [Security \& Compliance in Observability](#security--compliance-in-observability)
  - [Best Practices for Implementation](#best-practices-for-implementation)

## The Three Pillars of Observability

We have 3 types of telemetry data that we collect.

1. **Metrics** - Quantitative data like CPU usage, memory, request latency.
2. **Logs** - Textual records of events (with context) which help in figuring out (after an incident) what happened?
3. **Traces** - Show request flow across services (important in microservices)

## Monitoring Types & Methods

| Monitoring Type         | Focus                              | Key Metrics                             |
| :---------------------- | :--------------------------------------------------- | :------------------------------------------------- |
| **Infrastructure** | Health & performance of underlying hardware/VMs.     | Host health, Disk (I/O, space), Memory, CPU, Network utilization. |
| **Application (APM)**| Performance of software applications.                | Code-level performance, Errors, Bottlenecks, Transaction tracing, Throughput, Latency. |
| **Network** | Health & availability of network infrastructure.     | Latency, Packet loss, Connection status, Bandwidth utilization, Traffic flow. |
| **Synthetic** | Proactive simulation of user interactions from external points. | Simulate user journeys, Availability checks (ping, curl), API monitoring. |
| **Real User (RUM)** | Performance data from actual user sessions (frontend). | Page load time, Rendering time, UX metrics, Client-side errors, Geographical performance. |

> We can do monitring with or without an Agent (software installed directly on monitored device).
>
> - **Agent-based Monitoring** is best for important servers, apps and deep insights.
> - **Agentless Monitoring** is best for network devices, basic monitoring and broad coverage.
> - **Hybrid** using Agentless for broad coverage, Agent-based for deep insights on critical systems.
>
> Our monitoring can either be **Blackbox** (simulating external behaviour of a system) vs. **Whitebox** (measuring internal states of the system).

## DevOps Observability Tools

1. **LGTM Stack**
   1. [Loki](/docs/observability/loki.md) for Logs
   2. [Grafana](/docs/observability/grafana.md) for Dashboards
   3. [Tempo](/docs/observability/tempo.md) for Traces
   4. [Prometheus](/docs/observability/prometheus.md) for Metrics
      1. [Mimir](/docs/observability/mimir.md) as Long-term storage
      2. [Alertmanager] for Alerts
2. **ELK Stack**
   1. Elasticsearch for Indexing/Search
   2. Logstash for Data Pipeline
   3. Kibana for Visualization
   4. Beats for Lightweight Agents
3. **All-in-One Observability**
   1. Datadog
   2. New Relic
   3. Dynatrace
   4. Splunk
   5. Sumo Logic
   6. Honeycomb
4. **Cloud Stacks**
   1. AWS
      1. CloudWatch (metrics/logs/alerts)
      2. X-Ray (traces)
      3. CloudFront + Athena (querying)
   2. Azure
      1. Monitor
      2. Application Insights (APM & traces)
      3. Log Analytics (query engine)
   3. GCP
      1. Operations Suite (formerly Stackdriver)
      2. Cloud Trace / Logging / Monitoring
5. **Others**
   1. [OpenTelemetry](/docs/observability/opentelemetry.md) for Instrumentation and Collection standard
   2. [Vector] for Data Pipeline
   3. [InfluxDB] as Time-Series database
   4. [ClickHouse] as Columnar database for Analysis
6. Self-Hosted
   1. SigNoz (open source alternative to Datadog)
   2. Zabbix for legacy infra monitoring
   3. Netdata for system health monitoring
   4. VictoriaMetrics + VictoriaLogs (alternatives to Prometheus and Loki)

## Advanced Concepts

### Alerting Strategies

1. **Static Thresholds** - Trigger alerts when a metric goes over a fixed value.
2. **Rate of Change** - Alert when a value changes quickly over time, even if it's not past a hard threshold yet.
3. **Anomaly Detection** - Use statistical or machine learning models to detect abnormal behavior.
4. **Notification Channels** - Where alerts go once triggered (Slack, Teams, Email, PagerDuty)

> We should avoid alert fatigue: prioritize alerts (critical vs warning)

### Correlation & Enrichment

1. **Log/Metric/Trace Enrichment** - Add context to your telemetry to make it actionable/findable/understandable.
2. **Correlation for Root Cause Analysis (RCA)** - Link telemetry data together like a log error should link to the trace that caused it OR a spike in latency should link to the exact service in the trace path.

### Security & Compliance in Observability

- **Sensitive Data Handling** - Mask PII (emails, tokens, passwords) in logs.
- **Access Control** - Limit who can view logs and metrics.
- **Audit Trails** - Track who accessed what log data or dashboards.
- **Log Retention Policies** - Keep logs: short term: 7–30 days for high volume or Long term: archive to S3/Glacier or BigQuery for audits.

### Best Practices for Implementation

1. *Structured Logging* - Use **JSON or key-value pairs** as it is easy to search and parse in tools.
2. *Centralized Aggregation* - Send logs and metrics to one place for analysis.
3. *Consistent Labeling* - Every metric/log/trace should have standard tags
4. *Instrumentation Strategy* - Use **OpenTelemetry SDKs** (or vendor SDKs like Datadog, New Relic).
