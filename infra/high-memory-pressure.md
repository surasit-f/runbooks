# HighMemoryPressure

## Overview
- Service: node
- Severity: warning

---

## What Happened
Available memory < 15%

---

## Impact
- OOM kill risk
- System instability

---

## Investigation

```bash
free -m
