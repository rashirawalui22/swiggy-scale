# Swiggy Scale Simulation

A production-scale reliability analysis of a food delivery platform facing a World Cup Final traffic surge.

## Scenario

SwiftEats is a monolithic food delivery application consisting of:

- Single Node.js Express server
- Single PostgreSQL database
- No cache layer
- No CDN
- Synchronous payment processing

At 8 PM IST during an India vs Pakistan World Cup Final, a 50% discount notification is sent to 180 million users.

Expected CTR: 8%

Result:

- 14.4 million users open the app
- Massive traffic spike within 60 seconds
- System experiences cascading failures

---

## Documents

| Document | Description |
|-----------|-------------|
| FAILURE-CASCADE.md | Traffic analysis, capacity calculations, failure triggers, recovery timeline |
| ARCHITECTURE.md | Redesigned architecture capable of supporting massive scale |
| COST-ESTIMATE.md | AWS infrastructure pricing and ROI analysis |
| RUNBOOK.md | Incident response guide for on-call engineers |

---

## Key Findings

- Peak traffic exceeds 1.44 million RPS.
- PostgreSQL exhausts connections almost instantly.
- Payment API latency amplifies database failures.
- Static assets compete with APIs for bandwidth.
- Redis cache removes millions of unnecessary DB requests.

---

## Architecture Overview

The redesigned architecture introduces:

- CloudFront CDN
- Application Load Balancer
- Auto Scaling Node.js fleet
- Redis Cache
- PgBouncer
- PostgreSQL Read Replicas
- SQS Payment Queue
- Payment Workers

This transforms the system from a single point of failure into a resilient distributed architecture.

---

## Technology Context

- Node.js
- PostgreSQL
- Redis
- AWS  
- CloudFront
- SQS
- Auto Scaling