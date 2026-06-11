# Architecture Redesign

## Current Architecture

```text
Users
  |
  V
Node.js Monolith
  |
  V
PostgreSQL

⚠ Failure 1: Pool Exhaustion
⚠ Failure 2: Event Loop Saturation
⚠ Failure 3: Payment Amplification
⚠ Failure 5: NIC Saturation
```

## Redesigned Architecture

```text
                   CloudFront CDN
                          |
                          V

Users --> ALB --> Auto Scaling Node Fleet
                    |           |
                    |           |
                    V           V

               Redis Cache   SQS Queue
                    |            |
                    |            V
                    |      Payment Workers
                    |
                    V

                PgBouncer
                    |
                    V

           PostgreSQL Primary
              /          \
             /            \
            V              V

     Read Replica 1   Read Replica 2
```

---

## Component Details

### CloudFront CDN

Caches:

- Images
- CSS
- JavaScript

TTL:

24 hours

---

### ALB

Functions:

- SSL termination
- Health checks
- Traffic routing

---

### Auto Scaling Node Fleet

Baseline:

10 instances

Peak:

100+ instances

Scale Trigger:

CPU > 60%

---

### Redis Cache

Caches:

- Restaurant lists
- Menus
- Promo inventory

TTL:

5 minutes

---

### PgBouncer

Purpose:

Connection multiplexing

Allows thousands of sessions to share a small number of DB connections.

---

### PostgreSQL Read Replicas

Replicas:

2

Reads:

- Search
- Menus
- Restaurant listings

Writes:

Primary only

---

### SQS

Purpose:

Asynchronous payment processing

Order Flow:

Order → Queue → Worker → Razorpay

---

## Component Justification

| Component | Failure Prevented | Prevention Method |
|------------|------------------|-------------------|
| CloudFront | NIC Saturation | Static assets served from edge |
| ALB | Compute Overload | Traffic distribution |
| Auto Scaling | Event Loop Saturation | Horizontal scaling |
| Redis | DB Exhaustion | Cache reduces queries |
| PgBouncer | Pool Exhaustion | Connection multiplexing |
| Read Replicas | Read Overload | Read distribution |
| SQS | Payment Amplification | Async processing |
| Payment Workers | Payment Delay | Background execution |