# LowTrafficAnomaly

## Overview
- Service: mercury-api
- Severity: warning
- Type: anomaly

---

## What Happened
Traffic dropped below expected baseline

---

## Impact
- Possible outage
- Routing / DNS issues

---

## Investigation

### Metrics
```promql
sum(rate(http_requests_total[5m]))
