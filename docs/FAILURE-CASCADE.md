# Failure Cascade Analysis

## 1. Traffic Simulation Math

### Users Opening App

Total users notified = 180,000,000

CTR = 8%

Active users:

180,000,000 × 0.08 = 14,400,000 users

### API Calls Per User

Typical user journey:

- Home Feed = 1
- Restaurant List = 2
- Restaurant Detail = 1
- Cart = 1
- Promo Validation = 1

Total = 6 API calls

### Peak RPS

Peak RPS = (Active Users × Calls Per User) / 60

Peak RPS = (14,400,000 × 6) / 60

Peak RPS = 1,440,000 RPS

---

## 2. Component Capacity Numbers

### PostgreSQL

Configured:

max_connections = 100

Assumptions:

- 90% requests require DB access
- Query time = 50ms
- 10% payment traffic
- Payment hold time = 2s

Non-payment RPS:

1,440,000 × 0.90 = 1,296,000

Connections held:

1,296,000 × 0.05 = 64,800

Payment RPS:

1,440,000 × 0.10 = 144,000

Connections held:

144,000 × 2 = 288,000

Total:

64,800 + 288,000 = 352,800

Available:

100

Result:

PostgreSQL connection pool exhausts almost instantly.

---

### Node.js Event Loop

Capacity:

12,000–15,000 RPS

Incoming:

1,440,000 RPS

Overload:

~96× capacity

---

### Memory

Server:

1 CPU, 4 GB RAM

At approximately 15,000 queued requests:

Heap exceeds available memory

OOM crash becomes likely

---

## 3. Failure Cascade

### Failure 1 – PostgreSQL Pool Exhaustion (CRITICAL)

Trigger:

~394 RPS

User Impact:

- Database timeouts
- 500 errors
- Failed API responses

Causes Next:

Application request backlog

---

### Failure 2 – Node.js Event Loop Saturation (CRITICAL)

Trigger:

15,000 RPS

User Impact:

- APIs hang
- Extremely slow responses

Causes Next:

Memory growth and process crash

---

### Failure 3 – Synchronous Payment Amplification (HIGH)

Trigger:

Payment latency > 2s

User Impact:

- Failed payments
- Duplicate retries

Causes Next:

Additional DB connection pressure

---

### Failure 4 – Promo Code Race Condition (HIGH)

Trigger:

Mass simultaneous promo redemption

User Impact:

- Discount accepted then rejected
- Inconsistent cart totals

Causes Next:

Database lock contention

---

### Failure 5 – Static Asset NIC Saturation (HIGH)

Trigger:

Images and APIs share same server

User Impact:

- Images fail to load
- Application appears broken

Causes Next:

Network bottleneck and higher latency

---

## 4. Timeline

| Time | Event |
|--------|--------|
| T+0s | Notification sent |
| T+5s | Massive traffic spike begins |
| T+10s | PostgreSQL reaches max connections |
| T+15s | Database timeouts appear |
| T+20s | Node request queue grows |
| T+30s | Event loop saturation |
| T+45s | Payment failures appear |
| T+60s | Service mostly unavailable |
| T+5m | Engineering team alerted |
| T+15m | Emergency mitigation begins |
| T+30m | Scaling attempts |
| T+45m | Partial recovery |
| T+1h | Payments stabilize |
| T+90m | Database recovers |
| T+2h | Full recovery |