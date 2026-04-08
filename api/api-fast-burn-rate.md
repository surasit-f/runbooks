# APIFastBurnRate

## Overview
- Service: mercury-api
- Severity: critical
- Type: burn-rate

---

## What Happened
5xx error rate > 5% within 5 minutes  
Indicates rapid error budget consumption

---

## Impact
- Users receive server errors
- Possible outage

---

## Investigation

### Dashboard
http://grafana:3000/d/api-overview

### Metrics
```promql
sum(rate(http_requests_total{status_class="5xx"}[5m]))
/
sum(rate(http_requests_total[5m]))
