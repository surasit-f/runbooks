# Runbook: HighInFlightRequests

## Alert Name

**HighInFlightRequests**

## Description

จำนวน **in-flight HTTP requests** (request ที่กำลังประมวลผลอยู่) สูงเกิน threshold ที่กำหนดอย่างต่อเนื่อง

โดยทั่วไปหมายถึง:

* ระบบเริ่ม “ค้าง” หรือ “ประมวลผลไม่ทัน”
* latency กำลังเพิ่มขึ้น
* มีโอกาสเกิด timeout / 5xx ตามมา

---

## Impact

* Response time สูง (latency degradation)
* Throughput ลดลง
* User experience แย่ลง
* อาจ cascade ไปเป็น:

  * HighLatency
  * HighErrorRate
  * ServiceDown

---

## How to Investigate

### 1. ตรวจสอบปริมาณ request (traffic)

ใน Grafana:

```promql
rate(http_requests_total{service="mercury-api"}[1m])
```

ถ้า traffic spike → อาจเป็น load จริง

---

### 2. ตรวจสอบ latency

```promql
histogram_quantile(0.95,
  rate(http_request_duration_seconds_bucket{service="mercury-api"}[1m])
)
```

ถ้า p95 สูง → bottleneck ภายในระบบ

---

### 3. ตรวจสอบ error rate

```promql
rate(http_requests_total{service="mercury-api",status_class="5xx"}[1m])
```

ถ้า error เริ่มขึ้น → ระบบเริ่ม overload

---

### 4. ตรวจสอบ resource (infra)

ดูจาก Prometheus:

* CPU usage
* Memory pressure
* Disk I/O

---

### 5. ตรวจสอบ external dependency

* Database latency
* External API (`external_api_up`)
* Network

dependency ช้า → request ค้าง → in-flight พุ่ง

---

## Common Root Causes

| Cause                         | Description                    |
| ----------------------------- | ------------------------------ |
| Traffic spike              | request เข้ามาพร้อมกันจำนวนมาก |
| Slow DB query              | query ช้า ทำให้ request ค้าง   |
| External API slow          | dependency latency สูง         |
| Thread/connection pool ตัน | worker ไม่พอ                   |
| Deadlock / contention      | resource lock                  |
| Memory leak / GC pause     | app stall                      |

---

## Mitigation Steps

### ระยะสั้น (Immediate)

1. **Scale service**

   * เพิ่ม instance / pod
   * เพิ่ม concurrency

2. **Throttle traffic**

   * rate limit
   * queue request

3. **Restart service (ถ้าจำเป็น)**

   * กรณี memory leak / deadlock

---

### ระยะกลาง

* optimize DB query
* เพิ่ม connection pool
* caching layer (Redis)

---

### ระยะยาว

* implement circuit breaker
* async processing (queue)
* load shedding
* autoscaling policy

---

## Related Alerts

* HighLatencyP95
* APIHighErrorRateFast
* HighCPUUsage
* ServiceDown

---

## Dashboard

* API Overview Dashboard
* Node Metrics Dashboard

---

## References

* Internal runbook repo
* SLO dashboard

---

## Notes

* ค่า threshold ต้องสัมพันธ์กับ capacity ของระบบ
* ควร monitor คู่กับ latency + error rate เสมอ

---

## Resolution Criteria

Alert ถือว่า resolved เมื่อ:

* `http_requests_in_flight` ลดลงต่ำกว่า threshold
* latency กลับสู่ปกติ
* error rate ไม่เพิ่มขึ้น
