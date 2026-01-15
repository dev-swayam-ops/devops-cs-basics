# 10 - Scheduling and Performance Solutions

## Easy Solutions (1-5)

### Solution 1: Check Current Load

**Command**:
```bash
uptime
```

**Output Example**:
```
14:30:45 up 3 days, 2:15, 2 users, load average: 0.45, 0.50, 0.48
```

**Analysis**:
- Load: 0.45, 0.50, 0.48 (1, 5, 15 min)
- CPU count: `nproc` = 4 cores
- Ratio: 0.45/4 = 0.11 (11% utilization)
- Status: low load

---

### Solution 2: Monitor Load in Real-Time

**Command**:
```bash
watch -n 1 uptime
```

**Output Example**:
```
Every 1.0s: uptime                 Thu Jan 15 14:30:50 2026
14:30:50 up 3 days, 2:15, load average: 0.45, 0.50, 0.48
```

**Observation**:
- Refreshes every 1 second
- Load changes as processes run/finish
- Stable system: smooth changes
- Busy system: rapid increases

---

### Solution 3: Check Process Priorities

**Command**:
```bash
ps -o pid,nice,cmd | head -5
```

**Output Example**:
```
PID  NI CMD
1    0  /sbin/init
2    0  [kthreadd]
12   0  /lib/systemd/systemd-journald
256  10 sshd
```

**Analysis**:
- Default nice: 0
- Range: -20 (highest) to 19 (lowest)
- Lower nice = higher priority
- Root can go negative

---

### Solution 4: Start Process with Nice

**Command**:
```bash
nice -n 10 sleep 100 &
ps -o pid,nice,cmd | grep sleep
```

**Output Example**:
```
[1] 5678
PID  NI CMD
5678 10 sleep 100
```

**Notes**:
- Started with nice=10 (low priority)
- Negative nice requires root
- `nice -n -5` (higher priority) might need sudo

---

### Solution 5: Change Priority with Renice

**Commands**:
```bash
sleep 100 & PID=$!
renice -n 10 -p $PID
ps -o nice -p $PID
```

**Output Example**:
```
[1] 5679
5679: old priority 0, new priority 10
NI
10
```

**Explanation**:
- Changed from 0 to 10
- Takes effect immediately
- User can only increase nice (lower priority)
- Negative requires root

---

## Medium Solutions (6-10)

### Solution 6: Compare Process Scheduling

**Script**:
```bash
nice -n -10 sleep 300 &  # High priority
nice -n 10 sleep 300 &   # Low priority
# Monitor CPU in top
```

**Top Output**:
```
High priority: uses more CPU time
Low priority: less CPU time
Scheduler favors high priority
```

---

### Solution 7: Monitor Context Switches

**Command**:
```bash
cat /proc/self/status | grep ctxt
```

**Output Example**:
```
voluntary_ctxt_switches:        2345
nonvoluntary_ctxt_switches:     123
```

**Explanation**:
- Voluntary: process gave up CPU (I/O wait)
- Non-voluntary: scheduler forced (time slice)
- Many = high overhead
- Few = efficient

---

### Solution 8: Pin Process to CPU

**Commands**:
```bash
taskset -c 0 sleep 100 &
PID=$!
taskset -cp $PID          # Check affinity
taskset -cp 0,1 $PID      # Pin to 0,1
```

**Output Example**:
```
pid 5680's current affinity list: 0
pid 5680's new affinity list: 0,1
```

**Benefits**:
- Reduces cache misses
- Improves locality
- Better performance
- Useful for NUMA systems

---

### Solution 9: Load Balance Check

**Command**:
```bash
top        # Press 1 for per-CPU load
```

**Output Example**:
```
%Cpu0: 45.0 us, 5.0 sy
%Cpu1: 32.0 us, 8.0 sy
%Cpu2: 10.0 us, 2.0 sy
%Cpu3: 8.0 us, 1.0 sy
```

**Analysis**:
- Unbalanced: CPU0 > others
- Check process distribution
- Some processes pinned?
- Load balancer working?

---

### Solution 10: Simulate High Load

**Script**:
```bash
for i in {1..4}; do while true; do :; done & done
uptime      # Check load
ps aux | wc -l    # Count processes
pkill -f "while true" # Cleanup
```

**Output Example**:
```
Before: load average: 0.10
After: load average: 3.85  (4 CPUs busy)
Processes: increased
```

---

## Quick Reference

```bash
# Load and CPU
uptime; nproc

# Process priorities
ps -o nice,cmd
nice -n 10 sleep 100
renice -n 5 -p $PID

# CPU affinity
taskset -c 0 sleep 100
taskset -cp $PID

# Context switches
cat /proc/$PID/status | grep ctxt

# Performance
top (press 1)
watch uptime
```
