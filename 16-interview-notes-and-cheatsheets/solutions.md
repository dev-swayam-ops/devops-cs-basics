# 16 - Interview Notes and Cheatsheets - Solutions

## Easy Solutions (1-5)

### Solution 1: Process vs Thread

**Process**:
- Own memory space (isolated)
- Independent execution
- Heavier context switch
- More resources
- Safer (no shared memory issues)

**Thread**:
- Shared memory (within process)
- Lightweight creation
- Faster context switch
- Less resources
- Risk of race conditions

**When to use**:
- Threads: CPU parallelism, shared state
- Processes: Isolation, multi-user, different programs

---

### Solution 2: Memory Layout

**From high to low address**:
```
Stack (grows down)
↓
(gap)
↑
Heap (grows up)
BSS (uninitialized globals)
Data (initialized globals)
Text (code)
```

---

### Solution 3: CAP Theorem

**Tradeoff**:
- CA: Single machine (no network)
- CP: Consistent, unavailable in partition
- AP: Available, inconsistent in partition

**Examples**:
- CP: PostgreSQL (ACID), banking
- AP: DNS, web cache (eventual)

---

### Solution 4: SSH Authentication

**Flow**:
1. Client has private key
2. Server has public key
3. Client proves possession of private
4. SSH protocol: cryptographic proof
5. No password sent

**Advantages**:
- Stronger than passwords
- Automatable
- No password transmission
- Multiple keys per user

---

### Solution 5: HTTP Status Codes

**Retryable**:
- 2xx: Always success
- 3xx: Maybe (depends on code)
- 4xx: No (client error)
- 5xx: Yes (server error)

**Examples**:
- 200: OK (success)
- 404: Not Found (permanent, don't retry)
- 503: Service Unavailable (retry)

---

## Medium Solutions (6-10)

### Solution 6: Cache Strategy

**When to cache**:
- Read-heavy workload
- Repeated queries
- Expensive operations
- Acceptable staleness

**Cache-aside**:
1. Check cache
2. If miss: query database
3. Store in cache with TTL
4. Return to client

**Challenges**:
- Invalidation: when to clear?
- Stampede: many misses at once
- Memory: limited cache size
- Staleness: old data possible

---

### Solution 7: Debug Slow System

**Methodology**:
```
1. Check metrics (uptime, load, memory, disk)
2. Identify bottleneck (CPU, RAM, I/O)
3. Deep dive with appropriate tool
4. Locate root cause
5. Propose solution
```

**Tools**:
- CPU: top, perf, strace
- Memory: top, valgrind
- I/O: iotop, iostat, lsof
- Network: ss, netstat, ping

---

### Solution 8: Highly Available System

**Architecture**:
```
Users
  ↓
Load Balancer (active-passive)
  ↓
[Server 1] [Server 2] [Server 3]
  ↓
Shared DB (with replication)
  ↓
[Primary] [Replica 1] [Replica 2]
```

**Decisions**:
- Load balancing: round-robin or weighted
- Replication: sync (slower) or async (faster)
- Failover: automatic or manual
- Monitoring: health checks every N seconds

---

### Solution 9: Distributed Consensus

**Raft vs Paxos**:
- Raft: simpler, understandable
- Paxos: complex, more flexible

**Quorum**:
- N/2 + 1 ensures majority
- 3 nodes: quorum = 2
- Tolerates (N-1)/2 failures

**Leader election**:
1. Heartbeat from leader
2. If timeout: start election
3. Majority votes wins

**Partition**:
- Quorum side: continue
- Minority side: stop (avoid split-brain)

---

### Solution 10: Security Hardening

**SSH**:
- PermitRootLogin no
- PasswordAuthentication no
- PubkeyAuthentication yes

**Firewall**:
- Default deny all
- Allow needed ports (22, 80, 443)
- Log all drops

**Filesystem**:
- chmod 644 files, 755 dirs
- chmod 600 private keys
- Minimal permissions per user

**Secrets**:
- Environment variables (never code)
- Secret vault (Vault, sealed)
- Rotate regularly

**Updates**:
- Daily/weekly automatic updates
- Security patches immediately
- Test in staging first

**Monitoring**:
- Failed login attempts
- Port scans
- File modifications
- Permission changes

---

## Key Takeaways

```
Performance:
- Monitor CPU, memory, I/O
- Use appropriate tool for symptom
- Measure before optimizing

Reliability:
- Redundancy for critical systems
- Monitoring and alerting
- Disaster recovery plan

Security:
- Least privilege principle
- Encryption (transit and rest)
- Regular audits

Scale:
- Caching reduces DB load
- Sharding distributes data
- Load balancing distributes requests
```
