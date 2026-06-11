# Incident Response Runbook

## STEP 1 — DETECT

### CloudWatch Alarms

| Metric | Threshold | Severity |
|----------|-----------|-----------|
| CPUUtilization | >80% | Warning |
| CPUUtilization | >90% | Critical |
| DB Connections | >80 | Warning |
| DB Connections | >95 | Critical |
| ALB Response Time | >2s | Critical |
| Redis Hit Rate | <70% | Warning |
| SQS Queue Depth | >10000 | Critical |
| HTTP 5xx Rate | >5% | Critical |

---

## STEP 2 — TRIAGE

1. Check HTTP 5xx errors.

If elevated:

2. Check database connections.

If >95:

Root Cause = DB Pool Exhaustion

Else:

3. Check CPU.

If >90%:

Root Cause = Compute Saturation

Else:

4. Check SQS backlog.

If growing:

Root Cause = Payment Queue Failure

Else:

5. Check Redis hit rate.

If <70%:

Root Cause = Cache Failure

---

## STEP 3 — RESPOND

### DB Pool Exhaustion

Command:

```bash
aws rds describe-db-instances
```

Success:

Connections below 70%.

Owner:

Platform Team

---

### Compute Saturation

```bash
aws autoscaling set-desired-capacity \
--auto-scaling-group-name node-prod \
--desired-capacity 100
```

Success:

CPU below 60%.

---

### Payment Queue Backup

```bash
aws sqs get-queue-attributes
```

Increase worker count.

Success:

Queue depth continuously decreases.

---

### Redis Cache Miss Spike

```bash
redis-cli info stats
```

Warm cache.

Success:

Hit rate above 90%.

---

## STEP 4 — ROLLBACK

Rollback Criteria:

- Error rate above 10%
- Revenue impact confirmed
- Deployment identified as cause

Command:

```bash
kubectl rollout undo deployment/api
```

WARNING:

Never roll back database schema.

Only roll back application code.

---

## STEP 5 — POSTMORTEM

### Incident Summary

What happened?

---

### Timeline

Chronological sequence of events.

---

### Root Cause

Primary cause.

Contributing factors.

---

### Impact

- Revenue impact
- User impact
- Duration

---

### What Worked

Successful mitigations.

---

### What Failed

Detection gaps.

Process gaps.

---

### Action Items

| Action | Owner | Due Date |
|----------|---------|----------|
| | | |
| | | |