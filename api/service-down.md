# ServiceDown

## Overview
- Service: mercury-api
- Severity: critical
- Type: availability

---

## What Happened
Prometheus cannot scrape service (up == 0)

---

## Impact
- Service is unreachable
- Full outage

---

## Investigation

### Check container
```bash
docker ps
