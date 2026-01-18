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

**Job**: Decides which ready process gets CPU time. Fair allocation, prevent starvation, keep system responsive.

**Time Slices**: ~1-100ms per process (CFS scheduler adjusts dynamically). Timer interrupt triggers preemption.

**Ready Queue**: Processes waiting for CPU. Scheduler picks next from queue based on priority and fairness.

**Preemption**: Scheduler forcibly pauses process after time slice. Without it: one process monopolizes CPU.

**Multi-Core**: Each core has own queue. Kernel load-balances across cores. Process can migrate between cores.

**DevOps Concern**: Too many runnable processes = excessive context switching (thrashing). Monitor with `ps aux | wc -l` and `vmstat`.

### 2. Process Priority (Nice Value)

**Nice Range**: -20 (highest priority, gets most CPU) to 19 (lowest, least CPU). Default: 0.

**Commands**:
- `nice -n 10 command` - start with nice=10
- `renice -n 5 -p PID` - change running process
- Only root can lower nice (increase priority). Regular users can only raise it.

**Semantics**: Lower number = higher priority = more CPU time. Each level ~10% difference in allocation.

**Real-Time Priority**: -100 to -51 (kernel reserved). Preempts normal priority. Requires root. Use for latency-critical work.

**DevOps Usage**: Run batch jobs with `nice -n 10`. Ensure interactive services stay at default (0). Real-time for specialized use only.

### 3. Load Average

**Definition**: Average number of processes in ready queue (runnable) over time window.

**Interpretation**: Load relative to CPU count.
- Load = 1.0 with 1 CPU = fully utilized
- Load = 4.0 with 4 CPUs = fully utilized
- Load > CPU count = system overloaded (processes waiting for CPU)

**Three Numbers** (from `uptime`): 1-min, 5-min, 15-min averages.
- Compare trends: increasing 1-min vs 15-min = load rising
- Example: `load average: 0.25, 0.30, 0.35` = load increasing

**Monitor**:
```bash
uptime                    # Show current load
watch -n 1 'uptime'      # Real-time monitoring
cat /proc/loadavg         # Raw data
```

**Rule**: Ideal load ≈ CPU count. High load (>> cores) = thrashing or I/O wait.

**DevOps Metric**: Use alongside CPU usage. High load + low CPU = I/O wait. High load + high CPU = CPU-bound overload.

### 4. Context Switching

**What**: CPU switches from one process to another. Saves registers/state of current, restores next.

**Overhead**: 1000-10000 CPU cycles per switch. Main cost: TLB flush, cache invalidation.

**Too Many Switches (Thrashing)**: System spends time switching, not working. Symptoms: high load, low throughput.

**Monitor**:
```bash
cat /proc/stat           # Total system switches
cat /proc/[pid]/status   # Per-process switches (ctxt_switches line)
vmstat 1                 # Real-time context switch rate
```

**Types**:
- **Voluntary**: Process sleeps/blocks (efficient, saves CPU)
- **Involuntary**: Timer interrupt forces switch (necessary for fairness)

**Optimization**: Reduce runnable processes. Use single process + async I/O. Keep process on same core (CPU affinity).

**DevOps Concern**: High context switches + low throughput = thrashing. Reduce process count or increase resources.

### 5. CPU Affinity (CPU Pinning)

**What**: Bind process to specific CPU core(s). By default, process can run on any core.

**Why**: Process stays on same core → L1/L2 cache preserved. Core-to-core migration = cache miss (10-20% performance loss).

**Use Cases**: Databases (thread per core), real-time systems, NUMA optimization, reproducible testing.

**Commands**:
```bash
taskset -c 0,1 command           # Run on cores 0 and 1
taskset -pc 0 PID                # Pin running process to core 0
taskset -pc PID                  # Show current affinity
```

**NUMA Systems**: Memory latency varies by location. Pin process to cores near its memory for better performance.

**Trade-off**: Affinity reduces flexibility. If underutilized cores exist elsewhere, pinned process can't use them.

**DevOps**: Use for latency-critical services. For general servers, let kernel manage scheduling.

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

