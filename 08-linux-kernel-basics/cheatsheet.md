# 08-16 Cheatsheets (Consolidated)

## 08 - Kernel Cheatsheet

```
Boot: BIOS → GRUB → Kernel → Systemd

uname -a               Show all kernel info
lsmod                  List loaded modules
dmesg                  Kernel messages
/proc/sys/             Kernel parameters
sysctl -a              Show all parameters
cat /proc/interrupts   IRQ stats
```

## 09 - Signals & Syscalls Cheatsheet

```
Common Syscalls:
fork() - create process
exec() - replace process
read() - read file
write() - write file
open() - open file
close() - close file

Common Signals:
1=HUP, 2=INT, 3=QUIT, 15=TERM, 9=KILL

strace -c ls           Count syscalls
trap 'echo Caught!' TERM
```

## 10 - Scheduling Cheatsheet

```
nice -20 to +19        Process priority
nice -n 10 cmd         Run low priority
renice 5 -p PID        Change priority
taskset -c 0 cmd       Pin to CPU
perf stat              Get metrics
nproc                  CPU count
```

## 11 - Distributed Cheatsheet

```
CAP: Pick 2 of 3
- Consistency (all see same)
- Availability (always on)
- Partition tolerance (network fail)

Consensus: Raft, Paxos
Replication: Master-slave, Master-master
```

## 12 - Database Cheatsheet

```
EXPLAIN ANALYZE query  Show plan
CREATE INDEX key       Add index
SELECT COUNT(*) ...    Query count
VACUUM;                Optimize
mysqldump backup       Export
SHOW MASTER STATUS;    Replication status
```

## 13 - Cache/Queue Cheatsheet

```
redis-cli
SET key val EX 3600    Set with TTL
GET key                Get
DEL key                Delete
LPUSH queue item       Queue operation

Kafka:  Distributed streams
RabbitMQ: Message routing
Memcached: Distributed cache
```

## 14 - Security Cheatsheet

```
Encryption:
- Symmetric (AES): Fast
- Asymmetric (RSA): Slow
- Hash (SHA-256): One-way

TLS: Mutual authentication via certificates
2FA: Password + code/device
OAuth: Delegation token
```

## 15 - Observability Cheatsheet

```
ELK: Logs (Elasticsearch, Logstash, Kibana)
Prometheus: Metrics collection
Grafana: Dashboards
Jaeger: Distributed tracing
OpenTelemetry: Standard SDK

SLI: Measurable aspect
SLO: Target for SLI (e.g., 99.9%)
SLR: Recommended (internal)
```

## 16 - Interview Prep Cheatsheet

**OS Concepts**:
- Virtual memory & paging
- Process scheduling
- Memory hierarchy (cache, RAM, swap)
- File descriptors, inodes

**Networking**:
- TCP 3-way handshake
- DNS resolution
- HTTP status codes
- Load balancing

**Databases**:
- ACID properties
- Index types (B-tree, hash)
- Replication & sharding
- Query optimization

**Distributed**:
- CAP theorem
- Consensus algorithms
- Consistency models
- Failure detection

**DevOps Tools**:
- Docker, Kubernetes
- CI/CD: Jenkins, GitLab CI
- IaC: Terraform, Ansible
- Monitoring: Prometheus, Grafana
- Log aggregation: ELK, Splunk

---
