# 09-16: Quiz (Consolidated)

## Module 09 - Signals & Syscalls

**Q1-Q7**: read/write, boundary crossing, SIGTERM, Uncatchable, strace, Async, fork/exec
**Q8**: Fork creates child copy, exec replaces process image
**Q9**: Try SIGTERM first (graceful), SIGKILL if necessary (forced)
**Q10**: Signal handlers, signal masks, signal-safe functions

---

## Module 10 - Scheduling

**Q1-Q7**: CFS, Load, Nice, Priority, Switch, Affinity, Balance
**Q8**: CFS divides CPU time fairly among processes
**Q9**: Check `top`, identify CPU hog, use `renice` or kill
**Q10**: Monitor latency, measure throughput, profile with `perf`

---

## Module 11 - Distributed Systems

**Q1-Q7**: CAP=2, Eventual, Consensus, Replication, Raft, Heartbeat, Byzantine
**Q8**: Consistency hard to achieve, eventual consistency common
**Q9**: Split-brain requires quorum to prevent
**Q10**: Heartbeat monitoring, automatic failover, quorum-based election

---

## Module 12 - Databases

**Q1-Q7**: ACID, Index, Replica, Optimize, Shard, Backup, Failover
**Q8**: Atomicity (all-or-nothing), Consistency (valid state), Isolation (no interference), Durability (persists)
**Q9**: CREATE INDEX on frequently queried columns, check EXPLAIN
**Q10**: 3-2-1 rule (3 copies, 2 media, 1 offsite)

---

## Module 13 - Caching & Queues

**Q1-Q7**: Invalidate, TTL, Stampede, Write-through, Redis, Order, Dead letter
**Q8**: LRU eviction when cache full (least recently used removed first)
**Q9**: Cache stampede with lock: Use distributed lock when cache miss
**Q10**: Message queue for async, ordering important for consumers

---

## Module 14 - Security

**Q1-Q7**: Privilege, Encrypt, Auth, TLS, 2FA, HTTPS, Audit
**Q8**: Symmetric (shared key, fast), Asymmetric (public/private, slow)
**Q9**: Defense in depth = multiple layers, no single failure point
**Q10**: Regular patches, penetration testing, incident response plan

---

## Module 15 - Observability

**Q1-Q7**: Logs, Metrics, Traces, SLO, Alert, Profile, Correlation
**Q8**: ELK (logs), Prometheus (metrics), Jaeger (traces)
**Q9**: SLO 99.9% = 43 seconds downtime per month allowed
**Q10**: Set thresholds on metrics, alert on violations

---

## Module 16 - Interview Prep

**Key Topics Covered**:
- OS fundamentals & kernel
- Process & thread management
- Memory management & optimization
- Networking & DNS/HTTP
- Filesystems & storage
- Distributed systems
- Databases & indexing
- Caching & message queues
- Security & encryption
- Observability & monitoring

**Interview Question Types**:

**Technical Depth** (Q1-Q7):
- Core concept understanding
- Command/tool usage
- Trade-offs and limitations

**System Design** (Q8):
- Architecture for scale
- Technology choices
- Fault tolerance

**Problem Solving** (Q9):
- Trade-off analysis
- Design decisions
- Real-world constraints

**Incident Response** (Q10):
- Root cause analysis
- Mitigation strategies
- Preventive measures
- Learning from failure

**Sample Answers**:

**Q1**: What's the difference between eventual and strong consistency?
- Strong: All reads see latest write (slower, safer)
- Eventual: All nodes eventually consistent (faster, risky)
- Use strong for critical data (banking), eventual for social feeds

**Q2**: Design a system for 1M concurrent users
- Load balancer → multiple servers
- Cache layer (Redis)
- Database with replication/sharding
- Message queues for async tasks
- CDN for static content
- Monitoring & alerting

**Q3**: Capacity planning approach
- Baseline current metrics
- Forecast growth (linear, exponential)
- Identify bottlenecks
- Plan for 2x growth headroom
- Implement monitoring

**Q4**: Production incident - high latency
- Check metrics (CPU, memory, network)
- Identify slow query/process
- Check recent deployments
- Implement short-term fix
- Root cause analysis
- Deploy permanent fix
- Add monitoring/alerting

**Q5**: Database design decision
- Normalized: Fewer anomalies, complex queries
- Denormalized: Fast reads, update complexity
- Choose based on workload (OLTP vs OLAP)

---

## Quick Reference

**Must Know**:
- Virtual memory & paging
- Process lifecycle & signals
- Thread safety & synchronization
- TCP/IP & DNS
- Inode structure
- CAP theorem
- ACID properties
- Index types

**Must Do**:
- Explain clearly
- Discuss trade-offs
- Consider scale
- Plan for failure
- Monitor everything
- Optimize iteratively
- Document decisions
- Learn from incidents

---
