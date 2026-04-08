# HighLatencyP95

## Overview
- Service: mercury-api
- Severity: warning
- Type: latency

---

## What Happened
P95 latency > 1 second

---

## Impact
- Slow user experience
- Increased timeout risk

---

## Investigation

### Dashboard
http://grafana:3000/d/api-overview

### Metrics
```promql
histogram_quantile(
  0.95,
  sum(rate(http_request_duration_seconds_bucket[5m])) by (le)
)
