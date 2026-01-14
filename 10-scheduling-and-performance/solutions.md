# 10-16: Solutions, Cheatsheets & Quizzes (Final)

All solutions follow same pattern: concepts, commands, quiz answers provided in modules 08-09.

## Quick Solutions Reference

### Module 10 - Scheduling
- CFS divides CPU time fairly
- Load avg = processes waiting
- Nice: -20 to +19
- Perf for profiling

### Module 11 - Distributed
- CAP: Pick 2
- Eventual consistency common
- Raft consensus
- Quorum prevents split-brain

### Module 12 - Databases
- ACID properties
- B-tree indexing
- Master-slave replication
- 3-2-1 backup

### Module 13 - Caching
- LRU eviction
- TTL expiration
- Cache stampede lock
- Message ordering

### Module 14 - Security
- Least privilege
- Defense in depth
- Encrypt everything
- Regular patches

### Module 15 - Observability
- ELK for logs
- Prometheus for metrics
- Jaeger for traces
- SLO-based alerting

### Module 16 - Interview
- Study fundamentals
- Practice design
- Prepare examples
- Explain clearly

---

## Commands Quick Reference

### 10 - Scheduling
```bash
nice -n 10 cmd
renice 5 -p PID
taskset -c 0 cmd
perf stat cmd
stress-ng --cpu 4
```

### 11 - Distributed
```
Consensus: Raft, Paxos
Replication: Master-slave, Master-master
Monitoring: Heartbeat, health check
```

### 12 - Databases
```bash
EXPLAIN ANALYZE SELECT...
CREATE INDEX idx ON col
mysqldump db > bkp.sql
SHOW MASTER STATUS
```

### 13 - Caching
```bash
redis-cli SET key val EX 3600
memcached -p 11211
kafka-topics.sh --list
rabbitmqctl list_queues
```

### 14 - Security
```bash
openssl genrsa 2048
openssl s_client -connect :443
iptables -L -n
setenforce Enforcing
```

### 15 - Observability
```bash
curl localhost:9090/api/v1/query
journalctl -xe
grep pattern /var/log/
curl localhost:9200/index/_search
```

---

## All Quiz Answers (10-16)

**Module 10**: CFS, Load, Nice, Priority, Switch, Affinity, Load-balance
**Module 11**: CAP=2, Eventual, Consensus, Replication, Raft, Heartbeat, Byzantine
**Module 12**: ACID, Index, Replica, Optimize, Shard, Backup, Failover
**Module 13**: Invalidate, TTL, Stampede, Write-through, Redis, Order, Dead-letter
**Module 14**: Privilege, Encrypt, Auth, TLS, 2FA, HTTPS, Audit
**Module 15**: Logs, Metrics, Traces, SLO, Alert, Profile, Correlation
**Module 16**: Design, Scale, Trade-off, Architecture, Pattern, Monitor, Incident

---

## Key Concepts All Modules

1. **Concepts**: Understand the "why"
2. **Commands**: Know the tools
3. **Trade-offs**: Every design has pros/cons
4. **Real-world**: How does this apply?
5. **Monitoring**: Observe your system
6. **Security**: Design with security in mind
7. **Scale**: Think about growth
8. **Reliability**: Plan for failure

---

## Interview Readiness Checklist

- [ ] Understand OS fundamentals deeply
- [ ] Know process lifecycle and signals
- [ ] Comfortable with memory management
- [ ] Understand networking stack
- [ ] Know filesystem concepts
- [ ] Familiar with distributed systems theory
- [ ] Can design at scale
- [ ] Know database fundamentals
- [ ] Understand caching strategies
- [ ] Can discuss security trade-offs
- [ ] Know observability tools
- [ ] Can explain trade-offs clearly
- [ ] Have project examples ready
- [ ] Can solve problems step-by-step
- [ ] Understand DevOps tools ecosystem

---

## Interview Success Tips

1. **Listen carefully**: Understand the question
2. **Ask clarifying questions**: Scale? Constraints?
3. **Communicate clearly**: Explain your thinking
4. **Discuss trade-offs**: No perfect solution
5. **Consider failover**: How does it handle failure?
6. **Monitor everything**: What gets measured?
7. **Optimize iteratively**: Start simple, scale later
8. **Learn from failures**: Document incidents
9. **Ask for feedback**: How did you do?
10. **Keep learning**: Industry evolves

---

## Resources for Further Learning

- Linux man pages: `man 7 signal`, `man 2 fork`
- Papers: Raft, Paxos, CAP Theorem
- Books: "Systems Performance", "Designing Data-Intensive Applications"
- Practice: LeetCode system design, real projects
- Tools: Docker, Kubernetes, Terraform, Prometheus
- Communities: Stack Overflow, DevOps subreddits, local meetups

---
