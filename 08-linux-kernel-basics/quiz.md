# 08-16 Quiz (Consolidated)

## Module 08 - Kernel Quiz

**Q1-Q7**: Kernel, Kernel, Modules, Messages, Parameters, Syscalls, Tracing
**Q8**: BIOS→Bootloader→Kernel→Init
**Q9**: Security patches, old HW, specific features
**Q10**: Monitor dmesg, track panics, verify module loads

---

## Module 09 - Signals & Syscalls Quiz

**Q1-Q7**: 
1. System call, 2. Kernel boundary, 3. SIGTERM, 4. Uncatchable, 5. strace, 6. Async, 7. fork/exec

**Q8**: Fork creates copy, exec replaces image
**Q9**: Use SIGTERM first, then SIGKILL if needed
**Q10**: Trap handler, signal masks, safe functions

---

## Module 10 - Scheduling Quiz

**Q1-Q7**:
1. CFS, 2. Load avg, 3. Nice value, 4. Priority, 5. Context switch, 6. Affinity, 7. Load balance

**Q8**: CFS divides time fairly among processes
**Q9**: Check load, identify CPU hog (ps, top), renice or kill
**Q10**: Monitor latency, measure throughput, profile with perf

---

## Module 11 - Distributed Quiz

**Q1-Q7**:
1. CAP=pick 2, 2. Eventual, 3. Consensus, 4. Replication, 5. Raft, 6. Heartbeat, 7. Byzantine

**Q8**: Consistency hard across network, eventual common
**Q9**: Split-brain = partition breaks cluster, need quorum
**Q10**: Heartbeat monitoring, automatic failover, quorum election

---

## Module 12 - Database Quiz

**Q1-Q7**:
1. ACID, 2. Indexing, 3. Replication, 4. Query opt, 5. Sharding, 6. Backup, 7. Failover

**Q8**: ACID = atomicity, consistency, isolation, durability
**Q9**: CREATE INDEX on frequently queried columns
**Q10**: Backup: 3 copies, 2 media types, 1 offsite location

---

## Module 13 - Cache/Queue Quiz

**Q1-Q7**:
1. Cache invalidation, 2. TTL, 3. Stampede, 4. Write-through, 5. Redis, 6. Message order, 7. Dead letter

**Q8**: LRU = evict least recently used when full
**Q9**: Cache stampede = many requests after miss, mitigate with lock
**Q10**: Message queue for async, order matters for consumers

---

## Module 14 - Security Quiz

**Q1-Q7**:
1. Least privilege, 2. Encryption, 3. Authentication, 4. TLS, 5. 2FA, 6. HTTPS, 7. Audit

**Q8**: Symmetric = shared key fast, asymmetric = public/private key slow
**Q9**: Defense in depth = multiple layers, no single point failure
**Q10**: Regular patches, penetration testing, incident response plan

---

## Module 15 - Observability Quiz

**Q1-Q7**:
1. Logging, 2. Metrics, 3. Tracing, 4. SLO, 5. Alerting, 6. Profiling, 7. Correlation

**Q8**: ELK for logs, Prometheus for metrics, Jaeger for traces
**Q9**: SLO = 99.9% uptime means 43 seconds downtime per month
**Q10**: Collect metrics, set thresholds, alert on violations

---

## Module 16 - Interview Prep Quiz

**Key Topics**:
- Virtual memory, paging, segmentation
- Process creation, scheduling, signals
- Thread safety, synchronization
- TCP/IP, DNS, HTTP, CDN
- Filesystems, RAID, backup
- CAP theorem, consensus
- Encryption, authentication
- Databases: ACID, indexing, replication
- Caching: TTL, invalidation
- Monitoring: metrics, logs, traces

**Interview Format**:
- Q1-Q7: Domain knowledge (tech depth)
- Q8: System design (architecture)
- Q9: Trade-off analysis
- Q10: Real-world incident

**Example Q8**: Design system for 1M concurrent users
- Load balancing: Multiple servers
- Caching: Redis, CDN
- Database: Sharding + replication
- Monitoring: Prometheus + alerting
- Backup: Regular snapshots

**Example Q9**: Consistency vs Availability
- Strong consistency: Slower, safer (banking)
- Eventual consistency: Faster, risky (social media)
- Choose based on use case

**Example Q10**: Production incident - OOM killer
- Symptoms: Processes killed, service down
- Investigation: Check logs, memory usage
- Root cause: Memory leak in new code
- Fix: Identify and patch leak
- Prevention: Memory profiling in CI/CD

---

## Answer Keys

| Module | Answers |
|--------|---------|
| 08 | Kernel, Kernel, Modules, Messages, Parameters, Syscalls, Tracing |
| 09 | Call, Boundary, SIGTERM, Uncatchable, strace, Async, fork/exec |
| 10 | CFS, Load, Nice, Priority, Switch, Affinity, Balance |
| 11 | CAP, Eventual, Consensus, Replication, Raft, Heartbeat, Byzantine |
| 12 | ACID, Index, Replica, Optimize, Shard, Backup, Failover |
| 13 | Invalidate, TTL, Stampede, Write-through, Redis, Order, Dead letter |
| 14 | Privilege, Encrypt, Auth, TLS, 2FA, HTTPS, Audit |
| 15 | Logs, Metrics, Tracing, SLO, Alert, Profile, Correlation |
| 16 | Design, Trade-off, Pattern, Architecture, Scale, Monitor, Incident |

---
