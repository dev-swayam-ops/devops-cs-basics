# 09-16: Cheatsheets (Consolidated)

## Module 09 - Signals & Syscalls

**Common Syscalls**:
```
fork()      Create process
exec()      Replace process
read()      Read file
write()     Write file
open()      Open file
close()     Close file
brk()       Extend heap
mmap()      Map memory
wait()      Wait for child
kill()      Send signal
```

**Common Signals**:
```
1   SIGHUP      Hangup
2   SIGINT      Interrupt (Ctrl+C)
3   SIGQUIT     Quit (Ctrl+\)
9   SIGKILL     Kill (uncatchable)
15  SIGTERM     Terminate (graceful)
19  SIGSTOP     Stop (uncatchable)
20  SIGTSTP     Suspend (Ctrl+Z)
```

**Debugging**:
```bash
strace -c cmd          Count syscalls
strace -e trace=syscall cmd
trap 'echo Signal!' TERM
kill -l                List signals
```

---

## Module 10 - Scheduling & Performance

```bash
# Priorities
nice -n 10 cmd         Run low priority
renice -5 -p PID       Raise priority
getpriority process PID

# CPU Affinity
taskset -c 0,1 cmd     Pin to CPUs 0,1
taskset -p 0x3 PID

# Profiling
perf stat cmd
perf record cmd
perf report
perf top

# Load
uptime                 Check load
load average           Should be < CPU count
```

---

## Module 11 - Distributed Systems

```
CAP Theorem:
- Consistency: All see same data
- Availability: System always responds
- Partition: Works with network split
→ Pick any 2

Replication:
- Master-slave: One master, many slaves
- Master-master: Multiple masters
- Multi-master: All masters

Consensus:
- Raft: Log replication
- Paxos: Complex but powerful
- Quorum: Majority must agree
```

---

## Module 12 - Databases

```sql
-- Query optimization
EXPLAIN ANALYZE SELECT * FROM users WHERE id=1;
CREATE INDEX idx_id ON users(id);

-- Replication
SHOW MASTER STATUS;
SHOW SLAVE STATUS;
SET GLOBAL read_only = ON;

-- Backup
mysqldump -u root db > backup.sql
mysql -u root db < backup.sql

-- Monitoring
SHOW PROCESSLIST;
SHOW ENGINE INNODB STATUS;
```

---

## Module 13 - Caching & Queues

```bash
# Redis
redis-cli
SET key value EX 3600
GET key
DEL key
INCR counter
LPUSH queue item

# Memcached
memcached -p 11211
telnet localhost 11211
set key 0 3600 5
value

# Kafka
kafka-topics.sh --list
kafka-console-producer.sh
kafka-console-consumer.sh

# RabbitMQ
rabbitmqctl list_queues
amqp-consume
amqp-publish
```

---

## Module 14 - Security

```bash
# Encryption
openssl genrsa -out private.key 2048
openssl req -new -x509 -key private.key -out cert.pem

# TLS
openssl s_client -connect server:443
curl -v https://server

# Firewall
iptables -L -n
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
ufw enable
ufw allow 22

# SELinux
setenforce Enforcing
getenforce
chcon -t type_t file
```

---

## Module 15 - Observability

```bash
# ELK
curl -X POST localhost:9200/index/_doc -d '{"data":"value"}'

# Prometheus
curl localhost:9090/api/v1/query?query=metric

# Grafana
http://localhost:3000

# Logging
journalctl -xe
tail -f /var/log/syslog
grep error /var/log/app.log

# Tracing
opentelemetry-otlp-exporter
jaeger-agent

# Alerting
alertmanager
prometheus alerts.yml
```

---

## Module 16 - Interview Essentials

**OS & Kernel**:
- Virtual memory, paging, swap
- Process scheduling, priority
- Memory hierarchy, cache
- System calls, syscall overhead

**Processes & Threads**:
- fork(), exec() flow
- Process states, lifecycle
- Thread safety, race conditions
- Synchronization, deadlock

**Networking**:
- TCP 3-way handshake
- DNS resolution, caching
- HTTP methods, status codes
- Load balancing, CDN

**Storage**:
- Inode structure
- Hard links vs symlinks
- RAID levels (0,1,5,10)
- Backup strategies

**Distributed**:
- CAP theorem
- Consensus algorithms
- Consistency models
- Replication strategies

**Databases**:
- ACID properties
- Indexing (B-tree, hash)
- Query optimization
- Sharding, replication

**Monitoring**:
- Metrics, logs, traces
- SLI, SLO, SLR
- Alerting thresholds
- Root cause analysis

---
