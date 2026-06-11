# AWS Cost Estimate

## Baseline Infrastructure

### Node.js Fleet

10 × t3.large

Rate:

$0.0832/hour

Monthly:

0.0832 × 720 × 10

= $599

---

### PostgreSQL Primary

db.r6g.large

$0.252/hour

Monthly:

0.252 × 720

= $181

---

### PostgreSQL Read Replicas

2 × db.r6g.large

Monthly:

0.252 × 720 × 2

= $362

---

### Redis Cluster

cache.r6g.large

Monthly:

0.138 × 720

= $99

---

### Application Load Balancer

Estimated:

$25/month

---

### CloudFront

20 TB Transfer

≈ $1,700/month

---

### SQS

200 million messages

≈ $80/month

---

## Baseline Total

$3,046/month

≈ ₹2.6 lakh/month

---

## Peak Event Cost

### Additional Compute

90 extra t3.large

For 4 hours

0.0832 × 4 × 90

= $30

---

### Additional CloudFront Transfer

50 TB

≈ $4,250

---

## Peak Event Total

≈ $4,280

≈ ₹3.7 lakh

---

## Business Justification

Revenue loss rate:

₹4.2 crore/minute

45-minute outage:

₹4.2 crore × 45

= ₹189 crore

Monthly infrastructure cost:

≈ ₹2.6 lakh

Investing a few lakhs per month prevents potential losses approaching ₹189 crore during major traffic events. The ROI is overwhelmingly positive.