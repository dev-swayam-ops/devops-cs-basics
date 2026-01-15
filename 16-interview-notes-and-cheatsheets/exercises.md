# 16 - Interview Notes and Cheatsheets - Exercises

## Easy Exercises (1-5)

### Exercise 1: Explain Process vs Thread
**Objective**: Core OS concept.

**Task**:
- Process: Independent program, own memory
- Thread: Lightweight, shared memory
- When to use each?
- Context switch overhead: process > thread?

---

### Exercise 2: Describe Memory Layout
**Objective**: Understand address space.

**Task**:
- Text: code (read-only)
- Data: initialized globals
- Heap: dynamic allocation
- Stack: local variables
- Which grows up vs down?

---

### Exercise 3: Explain CAP Theorem
**Objective**: Distributed systems tradeoff.

**Task**:
- Consistency: all nodes same
- Availability: always responsive
- Partition: network split tolerance
- Pick 2 of 3, why?
- Examples for each?

---

### Exercise 4: Describe SSH Authentication
**Objective**: Security fundamentals.

**Task**:
- Private key: keep secret
- Public key: share widely
- Authentication flow: how verified?
- Advantages over passwords?

---

### Exercise 5: Explain HTTP Status Codes
**Objective**: Web basics.

**Task**:
- 2xx: success (200 OK, 201 Created)
- 3xx: redirect (301 Moved)
- 4xx: client error (404 Not Found)
- 5xx: server error (500 Internal)
- Which are retryable?

---

## Medium Exercises (6-10)

### Exercise 6: Design Cache Strategy
**Objective**: Performance optimization.

**Task**:
- When to cache?
- Cache-aside pattern: how?
- Cache invalidation: challenges?
- TTL: what value? (depends)
- Memory vs accuracy tradeoff?

---

### Exercise 7: Debug a Slow System
**Objective**: Troubleshooting methodology.

**Task**:
- First check: uptime, free, df, top
- Is CPU bottleneck?
- Is memory bottleneck?
- Is I/O bottleneck?
- Use: strace, perf, iotop
- Propose solutions

---

### Exercise 8: Design a Highly Available System
**Objective**: System design thinking.

**Task**:
- Single point of failure: bad
- Redundancy: multiple instances
- Load balancing: distribute
- Failover: automatic switching
- Database replication: async or sync?
- Monitoring: alerting

---

### Exercise 9: Explain Distributed Consensus
**Objective**: Distributed systems deep dive.

**Task**:
- Raft vs Paxos: differences?
- Quorum: N/2+1, why?
- Leader election: how?
- Network partition: what happens?
- Use cases: examples?

---

### Exercise 10: Security Hardening
**Objective**: Security best practices.

**Task**:
- SSH: disable password auth?
- Firewall: default deny?
- Filesystem: least privilege?
- Secrets: environment variables?
- Updates: how often?
- Monitoring: what to alert on?

---

## Submission Tips

1. Understand "why", not just "what"
2. Discuss tradeoffs (no perfect answer)
3. Mention tools you'd use
4. Show troubleshooting methodology
5. Ask clarifying questions
