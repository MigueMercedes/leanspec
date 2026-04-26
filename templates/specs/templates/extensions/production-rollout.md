## Rollout plan

> Extension: production-rollout. Enabled for risky features that need flags / gradual rollout.

- **Feature flag**: name and location (env var, settings field, DB column, LaunchDarkly key, etc.)
- **Default value**: typically `false` in prod until validated. State the default explicitly.
- **Gradual rollout**: % of users / tenants / cohorts. Concrete plan: "5% week 1, 25% week 2, 100% week 3" or pilot list of specific accounts.
- **Kill-switch**: how to disable quickly if there is an incident. Who has access. Mean time to disable (target: <5 min).
- **Success metric**: what you measure to decide GA. Example: "error rate < 0.1%, p95 latency < 200ms, conversion delta within ±2pp of control over 7 days".
- **Sunset of flag**: once GA, when does the flag get removed? (Avoid permanent flags accumulating.)
