# 08-16: Consolidated Solutions & Cheatsheets

## Module 08 - Kernel: Cheatsheet & Quiz

**Boot Sequence**: BIOS → Bootloader (GRUB) → Kernel → Init (systemd)

**Key Commands**:
```bash
uname -r               # Kernel version
lsmod                  # Loaded modules
dmesg                  # Kernel messages
sysctl vm.swappiness   # View parameter
echo 10 | sysctl -p    # Set parameter
```

**Q1-Q7**: Kernel mode, Kernel, Modules load on demand, Kernel messages, Parameters, System calls, Kernel tracing
**Q8**: BIOS→Bootloader→Kernel→Init
**Q9**: Security patches, old hardware, specific features
**Q10**: Monitor dmesg, track panics, check module loads

---

## Module 09 - Signals & Syscalls: Solutions

**System Calls**: Request kernel service from user space
- Process: fork(), exec(), exit(), wait()
- File: open(), read(), write(), close()
- Memory: brk(), mmap()

**Signals**: Async notifications
- SIGTERM (15): Graceful termination
- SIGKILL (9): Force kill (uncatchable)
- SIGSTOP (19): Suspend (uncatchable)
- SIGUSR1/2: User-defined

**Commands**:
```bash
strace -c command      # Count syscalls
kill -l                # List signals
trap handler SIGNAL    # Handle signal
```

**Quiz**: SIGTERM=graceful, SIGKILL=forced, trap catches, both uncatchable, 60-100 calls

---

## Module 10 - Scheduling: Cheatsheet

**Scheduler**: CFS (Completely Fair Scheduler)

```bash
nice -n 10 command     # Run with priority 10
renice 5 -p PID        # Change running process
taskset -c 0 command   # Pin to CPU 0
perf stat command      # Performance metrics
stress-ng              # Load testing
```

**Load Average**: How many processes waiting for CPU
- <CPU_count: OK
- >CPU_count: Overloaded

**Latency vs Throughput**: Trade-off in scheduling

---

## Module 11 - Distributed: Key Concepts

**CAP Theorem**: Pick 2 of 3
- Consistency: All see same data
- Availability: System always responding
- Partition tolerance: Works with network split

**Consistency Models**:
- Strong: All reads see latest write
- Eventual: Eventually consistent
- Causal: Respects causality

**Consensus**: Agreement among nodes (Raft, Paxos)

---

## Module 12 - Databases: Commands

```bash
EXPLAIN ANALYZE SELECT * FROM users;  # Query plan
CREATE INDEX idx_email ON users(email);
mysqldump -u root -p db > backup.sql
mongodump --db mydb
```

**Replication**: Master-slave, master-master
**Sharding**: Distribute data by key
**Backup**: 3-2-1 rule (3 copies, 2 media, 1 offsite)

---

## Module 13 - Caching: Tools & Patterns

**Redis**:
```bash
redis-cli
SET key value EX 3600     # Set with TTL
GET key
DEL key
ZADD myzset 1 member
```

**Queue Systems**:
- RabbitMQ: Message routing
- Kafka: Distributed streaming
- SQS: AWS simple queue

**Patterns**:
- Write-through: Write to cache & DB
- Write-back: Write to cache, async to DB
- Cache-aside: App checks cache, misses load from DB

---

## Module 14 - Security: Essentials

**Auth Methods**:
- Passwords: Weak
- 2FA: Stronger
- OAuth: Delegation
- Certificates: Asymmetric

**Encryption**:
- Symmetric (AES): Fast, shared key
- Asymmetric (RSA): Slow, public/private
- Hashing: One-way (bcrypt, SHA-256)

**TLS Handshake**: ClientHello → ServerHello + Cert → KeyExchange → DataTransfer

---

## Module 15 - Observability: Stack

**Logging**: ELK (Elasticsearch, Logstash, Kibana), Splunk

**Metrics**: Prometheus (scrape), Grafana (visualize)

**Tracing**: Jaeger, Zipkin (OpenTelemetry standard)

**Alerting**: Thresholds on metrics, log patterns

**SLI**: Measurable aspect (uptime, latency)
**SLO**: Target for SLI (99.9% uptime)

---

## Module 16 - Interview Prep

**Must Know**:
- Virtual memory, pagination
- Context switching, scheduling
- Process lifecycle, signals
- Thread safety, synchronization
- TCP/IP, DNS, HTTP
- Filesystems, inodes, RAID
- CAP theorem, consensus
- Encryption, authentication

**Practice**:
- System design (scale to 1M users)
- Technical depth (deep dive any topic)
- Incident response
- Code examples

---

## Universal Quiz Answers (All Modules)

**08**: Kernel, Kernel, Modules, Messages, Parameters, Syscalls, eBPF
**09**: read/write, SIGTERM, trap, strace, 100+ calls
**10**: CFS, Load=processes waiting, Latency=response time
**11**: CAP=pick 2, Consistency hard, Raft consensus
**12**: ACID, B-tree indexing, Replication
**13**: Redis, Hash tables, Message ordering
**14**: Least privilege, Defense in depth, Regular updates
**15**: ELK, Prometheus, Jaeger
**16**: Virtual memory, Scheduling, CAP theorem

---
