# 10 - Scheduling and Performance

## What You'll Learn

- Process scheduling fundamentals
- CPU scheduling algorithms
- Process priorities (nice, renice)
- Load average and system load
- Context switching
- Performance measurement
- Process affinity (CPU pinning)

## Prerequisites

- Completed **09-system-calls-and-signals**
- Understanding of processes and CPU
- Basic performance concepts

## Key Concepts

### 1. Process Scheduler
- Time slices (quantum): ~10ms per process
- Ready queue: waiting processes
- Runnable state: waiting for CPU
- Preemption: scheduler takes CPU back

### 2. Priority (Nice)
```
Nice range: -20 (highest) to 19 (lowest)
Default: 0
Lower nice = higher priority
Real-time: -100 to -51 (kernel reserved)
```

### 3. Load Average
```
Load 1.0 = 1 CPU fully used
Load 2.0 = 2 CPUs fully used (or 1 CPU overloaded)
Load 0.5 = 1 CPU half used
Numbers: 1-min, 5-min, 15-min averages
```

### 4. Context Switch
- Save CPU state
- Load new process state
- Expensive operation
- More switching = worse performance

### 5. CPU Affinity
- Pin process to specific CPU
- Reduce cache misses
- Improve performance

## Hands-on Lab: Scheduling and Load

### Lab Overview
Monitor scheduling, adjust priorities, measure load.

### Lab Commands

```bash
# 1. Show load average
uptime

# Expected:
# 12:30:45 up 2 days, load average: 0.25, 0.30, 0.35

# 2. Watch load in real-time
watch -n 1 uptime

# Expected: (refreshes every second)

# 3. Check process priority
ps -o pid,nice,cmd | head

# Expected:
# PID  NI CMD
# 1    0  /sbin/init
# 2    0  [kthreadd]

# 4. Start process with nice
nice -n 10 sleep 100 &

# Expected: Process starts with nice=10

# 5. Change priority (renice)
renice -n 5 -p [PID]

# Expected: (priority updated)

# 6. Show CPU context switches
cat /proc/stat | head -1

# Expected:
# cpu 10132153 290696 3084719 26399615...

# 7. Monitor context switches per process
cat /proc/[PID]/status | grep ctxt

# Expected:
# voluntary_ctxt_switches: 1234
# nonvoluntary_ctxt_switches: 56

# 8. Pin process to CPU
taskset -c 0 sleep 100 &

# Expected: Process runs on CPU 0

# 9. Show CPU count
nproc

# Expected: 4 (or number of CPUs)

# 10. Detailed CPU info
cat /proc/cpuinfo | grep processor

# Expected:
# processor: 0
# processor: 1
# processor: 2
# processor: 3
```

## Validation

```bash
# Can you check load?
uptime && echo "✓ Load shown"

# Adjust priority?
nice -n 10 sleep 1 && echo "✓ Nice works"

# Monitor processes?
ps -o nice,cmd | head && echo "✓ Priorities shown"

# Know CPU affinity?
echo "✓ Affinity understood"
```

## Cleanup

```bash
# Kill background processes
pkill -9 sleep 2>/dev/null || true
```

## Common Mistakes

1. **Nice scale**: -20 is highest, 19 is lowest (counterintuitive)
2. **Load interpretation**: Depends on CPU count
3. **Real-time priority**: Requires root or capability
4. **Context switch**: Not always bad (I/O blocking)
5. **Affinity conflicts**: Can reduce parallelism

## Troubleshooting

| Problem | Solution |
|---------|----------|
| High load | Check: ps, identify hogs, kill or renice |
| Too many context switches | Check: process count, thread count |
| Low CPU usage | Check: I/O wait, blocking calls |
| Can't renice | Check: permissions, negative nice needs root |
| CPU not balanced | Use: taskset to redistribute |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for commands
4. Move to **11-distributed-systems-basics** after completion

## Additional Resources

- Scheduling: `man sched`, `man 2 sched_setaffinity`
- Nice: `man nice`, `man renice`
- Load: `man uptime`, `man 1 w`
- Performance: `man top`, `man iostat`

