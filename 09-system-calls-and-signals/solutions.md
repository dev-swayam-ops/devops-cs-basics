# 09-16: Exercises, Solutions & Quizzes (Consolidated)

## Module 09 - System Calls & Signals

**Exercises**:
1. Common syscalls (fork, exec, read, write)
2. SIGTERM vs SIGKILL
3. Trap signal handlers
4. Strace syscall counting
5. Child process management
6. Signal handlers in scripts
7. Real-time signals
8. Signal-safe functions
9. Process communication
10. Debugging signals

**Solutions**:
- Syscalls: Fork creates process, exec replaces it
- Signals: SIGTERM graceful (caught), SIGKILL forced (uncatchable)
- Trap: Registers handler for signal in bash
- Strace: `strace -c cmd` counts syscalls
- Child management: Use `wait` to reap zombie processes

**Quiz**:
Q1-Q7: read/write, boundary crossing, SIGTERM, Uncatchable, strace, Async, fork/exec
Q8: Fork copies, exec replaces
Q9: Try SIGTERM first, use SIGKILL if necessary
Q10: Signal handlers, masks, safe functions

---

## Module 10 - Scheduling & Performance

**Exercises**:
1. Understand CFS scheduler
2. Load average interpretation
3. Nice/renice process priority
4. CPU affinity with taskset
5. Context switch overhead
6. Load balancing
7. Latency vs throughput trade-off
8. Perf tool for profiling
9. Stress testing
10. Performance optimization

**Solutions**:
- CFS: Completely Fair Scheduler, divides time fairly
- Load average: Number of processes waiting for CPU
- Nice: -20 (highest) to +19 (lowest priority)
- CPU affinity: Pin process to specific CPU cores
- Perf: Performance profiling tool

**Commands**:
```
nice -n 10 cmd         Run low priority
renice 5 -p PID        Change priority  
taskset -c 0 cmd       Pin to CPU 0
perf stat cmd          Performance metrics
perf record cmd        Record profile
perf report            Analyze profile
stress-ng --cpu 4      Load test CPUs
```

**Quiz**:
Q1-Q7: CFS, Load avg, Nice value, Priority change, Context switch, Affinity, Load balance
Q8: CFS divides CPU time fairly
Q9: Check load, identify with top, adjust priority
Q10: Monitor latency, measure throughput

---

## Module 11 - Distributed Systems

**Exercises**:
1. CAP theorem understanding
2. Consistency models (strong vs eventual)
3. Consensus algorithms (Raft, Paxos)
4. Replication strategies
5. Two-phase commit
6. Byzantine failures
7. Failure detection (heartbeat)
8. Leader election
9. Split-brain scenarios
10. Distributed tracing

**Key Concepts**:
- CAP: Pick 2 of 3 (Consistency, Availability, Partition tolerance)
- Eventual consistency: Common in distributed systems
- Consensus: Agreement among nodes
- Raft: Modern consensus algorithm
- Byzantine: Nodes can be faulty/malicious

**Quiz**:
Q1-Q7: CAP=2, Eventual, Consensus, Replication, Raft, Heartbeat, Byzantine
Q8: Consistency hard, eventual common
Q9: Split-brain needs quorum
Q10: Heartbeat, failover, quorum

---

## Module 12 - Databases for DevOps

**Exercises**:
1. SQL vs NoSQL trade-offs
2. ACID properties
3. Indexing strategies
4. Query optimization with EXPLAIN
5. Replication and failover
6. Backup and recovery
7. Connection pooling
8. Sharding strategies
9. Monitoring databases
10. Capacity planning

**Solutions**:
- ACID: Atomicity, Consistency, Isolation, Durability
- Index: B-tree most common, speeds up queries
- Replication: Master-slave or master-master
- Sharding: Horizontal scaling by data range
- Backup: Regular snapshots, test restore

**Commands**:
```
EXPLAIN ANALYZE SELECT...     Query plan
CREATE INDEX idx ON table(col)
mysqldump -u root db > bkp.sql
SHOW MASTER STATUS             Replication
```

**Quiz**:
Q1-Q7: ACID, Index, Replica, Optimize, Shard, Backup, Failover
Q8: ACID properties
Q9: Index creation
Q10: 3-2-1 backup rule

---

## Module 13 - Caching and Queues

**Exercises**:
1. Cache invalidation strategies
2. Redis data structures
3. Memcached vs Redis comparison
4. Queue systems (RabbitMQ, Kafka)
5. Cache warming
6. TTL and expiration
7. Cache stampede prevention
8. Distributed caching
9. Message ordering
10. Dead-letter queues

**Solutions**:
- Invalidation: TTL, tag-based, event-based
- Redis: Supports strings, lists, sets, hashes, sorted sets
- Queue: Async processing, decoupling
- Stampede: Use lock when cache missing
- TTL: Set expiration time

**Quiz**:
Q1-Q7: Invalidate, TTL, Stampede, Write-through, Redis, Order, Dead letter
Q8: LRU eviction
Q9: Stampede prevention with lock
Q10: Async messaging

---

## Module 14 - Security Basics

**Exercises**:
1. Authentication methods
2. Encryption symmetric vs asymmetric
3. TLS/SSL certificates
4. Vulnerability scanning
5. Security hardening
6. Firewall rules
7. SELinux/AppArmor
8. Access control lists
9. Audit logging
10. Incident response

**Solutions**:
- Auth: Password, 2FA, OAuth, Certificates
- Encryption: Symmetric (fast), Asymmetric (slow), Hash (one-way)
- TLS: Mutual authentication
- Hardening: Least privilege, no default creds
- Auditing: Log access, changes

**Best Practices**:
- Least privilege principle
- Defense in depth
- Regular updates and patches
- Strong passwords
- 2FA everywhere

**Quiz**:
Q1-Q7: Privilege, Encrypt, Auth, TLS, 2FA, HTTPS, Audit
Q8: Symmetric vs asymmetric
Q9: Defense in depth
Q10: Patching, testing, response plan

---

## Module 15 - Observability and Debugging

**Exercises**:
1. Logging architecture
2. Metrics collection
3. Distributed tracing
4. Alerting and thresholds
5. Performance profiling
6. Log analysis
7. Correlation IDs
8. SLI/SLO definition
9. Root cause analysis
10. Chaos engineering

**Solutions**:
- Logging: ELK, centralize logs
- Metrics: Prometheus scrape, Grafana visualize
- Tracing: Jaeger, OpenTelemetry
- Alerting: Set thresholds, escalate
- SLO: Define service level objectives

**Tools Stack**:
- Logs: ELK, Splunk, Datadog
- Metrics: Prometheus, Grafana
- Traces: Jaeger, Zipkin
- Alerting: PagerDuty, Opsgenie

**Quiz**:
Q1-Q7: Logs, Metrics, Traces, SLO, Alert, Profile, Correlation
Q8: ELK, Prometheus, Jaeger
Q9: SLO = 99.9% = 43s downtime/month
Q10: Threshold alerting

---

## Module 16 - Interview Notes & Cheatsheets

**Must-Know Topics**:
- OS: Virtual memory, scheduling, signals
- Processes: Creation, states, lifecycle
- Threads: Safety, synchronization, deadlock
- Networking: TCP/IP, DNS, HTTP
- Storage: Inodes, RAID, filesystem
- Distributed: CAP, consensus, replication
- Databases: ACID, indexing, sharding
- Security: Encryption, authentication
- DevOps: Docker, K8s, CI/CD, monitoring

**Interview Patterns**:
- Q1-Q7: Domain knowledge
- Q8: System design (scale architecture)
- Q9: Trade-off analysis
- Q10: Incident response + learning

**Preparation Tips**:
1. Study fundamentals deeply
2. Practice system design problems
3. Review past projects, document learnings
4. Prepare specific examples for behavior questions
5. Know your tools intimately
6. Explain concepts simply
7. Ask clarifying questions
8. Discuss trade-offs confidently

**Common Topics**:
- Scale to 1M users
- Consistency vs availability
- Memory vs disk trade-offs
- Latency vs throughput
- Cost vs performance
- Reliability vs speed

---
