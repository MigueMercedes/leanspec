## Operational

> Extension: operational. Enabled when the feature has observability, alerting, or runbook needs.

- **Structured logs**: which events get logged, at which level, with which fields. Avoid logging PII unless masked.
- **Metrics**: counters / histograms / gauges to emit. Names follow your project convention.
- **Dashboards**: links to Grafana / Datadog / equivalent panels that should be updated or created.
- **Alerting**: which condition pages oncall, severity, runbook link. Avoid noisy alerts (>1/week non-actionable = bad).
- **Runbook**: if it breaks in prod, where is the playbook. Path in `docs/runbooks/`. If runbook doesn't exist yet, create it as part of this work.
- **SLO impact**: does this feature affect any SLO commitment (availability, latency)? If yes, document.
