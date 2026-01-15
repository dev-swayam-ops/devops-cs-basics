# 15 - Observability and Debugging Solutions

## Easy Solutions (1-5)

### Solution 1: View System Logs

**Command**:
```bash
journalctl -n 30
```

**Output Example**:
```
Jan 15 14:30:45 myhost systemd[1]: Started SSH Service
Jan 15 14:31:00 myhost kernel: audit: type=1400 audit(...)
Jan 15 14:31:15 myhost sshd[1234]: Accepted publickey for user
```

**Analysis**:
- Last 30 messages
- Timestamp: date and time
- Service name: systemd, kernel, sshd, etc
- Log message: action or event

---

### Solution 2: Follow Live Logs

**Commands**:
```bash
journalctl -f
# In another terminal:
logger "test message"
```

**Output**:
```
(message appears in journalctl -f)
```

**Benefit**: Monitor real-time system activity

---

### Solution 3: Search Logs

**Command**:
```bash
journalctl | grep -i error
# or
journalctl -p err
```

**Output Example**:
```
Jan 15 13:45:00 host sshd[5678]: Failed password
Jan 15 14:00:15 host kernel: error in module
```

---

### Solution 4: Log Levels

**Hierarchy**:
```
DEBUG (7): Very detailed
INFO (6): General information
WARN (4): Warnings
ERROR (3): Errors
CRITICAL (2): Critical
Default: INFO level
```

---

### Solution 5: Monitor Process

**Command**:
```bash
top -p [PID]
```

**Output**:
```
PID  %CPU  %MEM  VSZ    RSS
1234  0.0   0.2   45M    8M
```

**Interpretation**:
- %CPU: 0% (sleeping)
- %MEM: 0.2% (8MB of 4GB)
- Healthy process

---

## Medium Solutions (6-10)

### Solution 6: Trace System Calls

**Command**:
```bash
strace -e trace=open,read,write sleep 1
```

**Output**:
```
open("/lib/ld-linux-x86-64.so.2", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF...", 832) = 832
write(1, "...", 10) = 10
```

**Insight**: Files opened, read, written

---

### Solution 7: Count Calls

**Command**:
```bash
strace -c sleep 1
```

**Output**:
```
% time calls   name
20.0  100     read
15.0  50      write
10.0  200     mmap
```

**Analysis**: Most common syscalls and timing

---

### Solution 8: Trace Library Calls

**Command**:
```bash
ltrace -e printf bash -c 'echo hello'
```

**Output**:
```
printf("hello\n")   = 6
```

**Difference**: ltrace shows library calls, strace shows syscalls

---

### Solution 9: Detect Memory Leak

**Setup**:
```bash
python3 -c 'import time; a=[]; [a.append(list(range(10000))) for _ in range(100)]; time.sleep(60)'
```

**Monitor**:
```bash
watch 'ps -o pid,vsz,rss -p [PID]'
```

**Observation**:
```
VSZ and RSS growing continuously = leak
Stable = OK
```

---

### Solution 10: Performance Analysis

**Tools**:
```bash
# Overall timing
time slow_command

# System calls
strace -c slow_command

# Per-function time
ltrace -c slow_command

# Real-time
top -p [PID]

# Memory profiling
valgrind --tool=massif slow_command
```

**Approach**: Use tool matching bottleneck location

---

## Quick Reference

```bash
# View logs
journalctl -n 50
journalctl -f
journalctl -p err

# Trace execution
strace -c command
ltrace -c command

# Monitor
top -p PID
ps -eo pid,%cpu,%mem --sort=-%cpu

# Debug
gdb program
valgrind --leak-check=full program
```
