# APISlowBurnRate

## Overview
- Service: mercury-api
- Severity: warning
- Type: burn-rate

---

## What Happened
5xx error rate > 2% over 1 hour

---

## Impact
- Gradual degradation
- Long-term SLO risk

---

## Investigation

### Dashboard
http://grafana:3000/d/api-overview

### Metrics
```promql
sum(rate(http_requests_total{status_class="5xx"}[1h]))
/
sum(rate(http_requests_total[1h]))
