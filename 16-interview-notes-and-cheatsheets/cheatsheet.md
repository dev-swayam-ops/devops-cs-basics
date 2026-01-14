# 16 - Interview Notes and Cheatsheets: Combined Reference

## Core DevOps Concepts Interview Questions

### Operating Systems (Module 01)
**Q: Explain the difference between kernel mode and user mode.**
A: Kernel mode has full hardware access; user mode is restricted. System calls bridge the two.

**Q: What are the main OS responsibilities?**
A: Memory management, process scheduling, I/O handling, security, resource allocation.

### Process Management (Module 02)
**Q: What's the difference between fork() and exec()?**
A: fork() creates child process copy; exec() replaces process image with new program.

**Q: How do you handle zombie processes?**
A: Parent must call wait() to reap child exit status. Kill parent if hung.

### Threading & Concurrency (Module 03)
**Q: Explain race conditions and how to prevent them.**
A: Multiple threads accessing shared data unsynchronized. Prevent with locks (mutexes).

**Q: What causes deadlocks?**
A: Circular lock dependencies (Thread A waits for lock B, B waits for lock A).

### Memory Management (Module 04)
**Q: What's the difference between VSZ and RSS?**
A: VSZ = total virtual memory allocated. RSS = actual physical RAM in use.

**Q: How do page faults impact performance?**
A: Major faults (disk access) are very slow (~1000x slower than RAM access).

### Storage & Filesystems (Module 05)
**Q: Explain hard links vs symbolic links.**
A: Hard link = same inode, survives deletion. Symlink = points to filename, breaks on deletion.

**Q: What's in an inode?**
A: Ownership, permissions, timestamps, size, link count, block pointers.

### Networking (Module 06)
**Q: Explain the TCP 3-way handshake.**
A: SYN → SYN-ACK → ACK. Establishes connection before data transfer.

**Q: What layer does TCP operate on?**
A: Layer 4 (Transport layer) of OSI model.

### DNS & HTTP (Module 07)
**Q: How does DNS caching work?**
A: Resolvers cache results with TTL. Reduces lookups, improves performance.

**Q: Difference between HTTP/1.1 and HTTP/2?**
A: HTTP/2: multiplexing, binary framing, header compression, server push.

### Linux Kernel (Module 08)
**Q: What are loadable kernel modules?**
A: Code loaded into kernel without reboot. Useful for device drivers.

**Q: How does the kernel handle interrupts?**
A: IRQ handler runs, saves state, processes interrupt, restores state.

### Signals (Module 09)
**Q: Which signals can't be caught?**
A: SIGKILL (9) and SIGSTOP (19) are uncatchable.

**Q: How do you handle signals in a script?**
A: Use `trap` command: `trap 'handler_function' SIGTERM`

### Scheduling (Module 10)
**Q: What is load average?**
A: Average number of runnable processes. If > CPU count, system is overloaded.

**Q: How to change process priority?**
A: `renice -n 5 -p [PID]` or `nice -n 10 ./program`

### Distributed Systems (Module 11)
**Q: Explain CAP theorem.**
A: Choose 2 of: Consistency, Availability, Partition tolerance. No system has all 3.

**Q: What's eventual consistency?**
A: All replicas eventually converge to same state. Allows high availability.

### Databases (Module 12)
**Q: SQL vs NoSQL?**
A: SQL: structured, ACID, relationships. NoSQL: unstructured, eventual consistency, scalable.

**Q: How to optimize slow queries?**
A: Add indexes, analyze query plan (EXPLAIN), denormalize if needed.

### Caching (Module 13)
**Q: Cache invalidation strategies?**
A: TTL, event-based, versioning, scan-and-invalidate.

**Q: Redis vs Memcached?**
A: Redis: data structures, persistence, pub/sub. Memcached: simple key-value, distributed.

### Security (Module 14)
**Q: What's the difference between authentication and authorization?**
A: Authentication: WHO you are. Authorization: WHAT you can do.

**Q: Name common vulnerabilities.**
A: SQL injection, XSS, CSRF, broken auth, insecure deserialization.

### Observability (Module 15)
**Q: Three pillars of observability?**
A: Logs (events), Metrics (measurements), Traces (request paths).

**Q: What makes a good alert?**
A: Actionable, low false-positives, clear severity, escalation path.

---

## DevOps Tools Quick Reference

```
IaC: Terraform, Ansible, CloudFormation
CI/CD: Jenkins, GitLab CI, GitHub Actions
Containers: Docker, Kubernetes, Podman
Monitoring: Prometheus, Grafana, Datadog
Logging: ELK Stack, Splunk, CloudWatch
Databases: PostgreSQL, MongoDB, Redis
Message Queues: RabbitMQ, Kafka, SQS
```

---

## Common Interview Patterns

**System Design:**
- Start with requirements (functional, non-functional)
- Draw architecture diagram
- Discuss scaling strategies
- Identify single points of failure
- Trade-offs (consistency vs availability, etc.)

**Troubleshooting:**
- Gather information
- Form hypothesis
- Test hypothesis
- Fix root cause
- Prevent recurrence

**Performance:**
- Profile to identify bottleneck
- Measure before/after changes
- Focus on top bottleneck first
- Use appropriate tools for each layer

---

## Quick Command Reference

```bash
# System
uname -a                    # OS info
free -h                     # Memory
df -h                       # Disk
top                         # Processes
ps aux                      # All processes

# Network
ip addr show                # IP config
ss -tlnp                    # Listening ports
curl -v URL                 # HTTP debug
dig domain                  # DNS lookup

# Files
ls -la                      # Files with permissions
stat file                   # File metadata
find / -name pattern        # Search files
du -sh /path                # Directory size

# Permissions
chmod 644 file              # Set permissions
chown user:group file       # Change owner
umask 022                   # Default permissions

# Package Management
apt update && apt upgrade   # Debian/Ubuntu
yum update                  # RHEL/CentOS
pip install package        # Python packages

# Version Control
git clone URL               # Clone repo
git commit -am "message"    # Commit
git push origin branch      # Push
git log --oneline          # History

# Container
docker build -t name .      # Build image
docker run -d image        # Run container
docker ps                  # List containers
kubectl get pods           # List pods

# Cloud
aws s3 ls                  # List S3 buckets
aws ec2 describe-instances # List EC2 instances
gcloud compute instances list  # GCP instances
```

---

## Interview Preparation Checklist

- [ ] Understand OS concepts deeply (processes, memory, I/O)
- [ ] Know networking stack and troubleshooting
- [ ] Familiar with at least one cloud platform (AWS, GCP, Azure)
- [ ] Experience with containers and Kubernetes
- [ ] Monitoring/logging setup and best practices
- [ ] Security fundamentals (auth, encryption, secrets)
- [ ] Database scaling and optimization
- [ ] CI/CD pipeline design
- [ ] Infrastructure as Code (Terraform preferred)
- [ ] Able to explain system designs
- [ ] Comfortable with scripting (Bash, Python)
- [ ] Familiar with Git workflows

---
