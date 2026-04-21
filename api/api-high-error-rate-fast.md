# APIHighErrorRateFast

## Overview
- Service: mercury-api
- Severity: critical
- Type: burn-rate

---

## What Happened
5xx error rate > 5% over 5 minutes

---

## Impact
- Users receive server errors
- API may be partially or fully unavailable

---

## Investigation

### Dashboard
http://grafana:3000/d/api-overview

### Metrics
```promql
sum(rate(http_requests_total{status_class="5xx"}[5m]))
/
sum(rate(http_requests_total[5m]))
