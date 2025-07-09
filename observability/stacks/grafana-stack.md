# Grafana Stack

| Tool      | What it does                        |
| ---       | ---                                 |
| Grafana   | Dashboards                          |
| Alloy     | Collection + Processing + Exporting |
| Mimir     | Metrics                             |
| Loki      | Logs                                |
| Tempo     | Traces                              |
| Pyroscope | Profiles                            |

This is a very modern and scalable observability stack. That uses FOSS tools made  by Grafana Labs. Alloy + Mimir replaces the need for Prometheus and Alloy itself replaces Otel Collector and Various Exporters.

## Setup

When setting up via Containers. The file structure would look like this:

```tree
grafana-stack/
├── docker-compose.yml
├── .env                        # Environment variables
├── grafana/
│   └── provisioning/
│       ├── dashboards/
│       └── datasources/
│   └── grafana.ini
├── alloy/
│   └── config.alloy            # Main config
│   └── metrics.alloy           # (Optional) Split config
│   └── logs.alloy              # (Optional)
│   └── traces.alloy            # (Optional)
├── loki/
│   └── config.yaml
├── tempo/
│   └── tempo.yaml
├── mimir/                      # Optional (or prometheus/)
│   └── mimir.yaml
└── data/
    ├── grafana/                # Persistent data volumes
    ├── loki/
    ├── tempo/
    └── mimir/
```

