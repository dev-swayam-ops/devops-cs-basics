# 10 - Scheduling and Performance

## What You'll Learn

Master CPU scheduling and performance optimization:
- Scheduling algorithms and policies
- CPU load and utilization
- Context switching overhead
- Performance profiling and bottleneck identification
- CPU affinity and pinning
- Latency vs throughput tradeoffs
- Monitoring tools and metrics

## Prerequisites

- Completed **09-system-calls-and-signals**
- Understanding of processes and CPU
- Linux command-line skills

## Key Concepts

### 1. Scheduling Algorithms
- **FIFO**: First-in-first-out (no preemption)
- **Round-Robin**: Each process gets time slice
- **Priority Queue**: Higher priority processes first
- **Completely Fair Scheduler (CFS)**: Kernel default, fair CPU allocation
- **Multi-queue**: Different priority levels

### 2. Load Average
- 1-minute, 5-minute, 15-minute averages
- Represents runnable processes (on queue waiting CPU)
- Load of 1 = single CPU at 100%
- Load of 4 = quad CPU at 100% (or similar equivalent)

### 3. Context Switch Cost
- Switching out process state (registers, memory)
- TLB flush (address translation cache)
- CPU cache thrashing
- More switches = more overhead

### 4. Performance Metrics
- **CPU Utilization**: % of CPU in use
- **Throughput**: Operations/second
- **Latency**: Time for single operation
- **Cache hit rate**: Data in fast cache vs main memory

## Hands-on Lab: CPU Scheduling and Performance

### Lab Steps

```bash
# 1. Check load average
uptime
cat /proc/loadavg

# 2. View process priorities
ps aux -o pid,pri,nice,cmd | head -20
nice -n 10 sleep 100

# 3. Change process priority
renice -n 5 -p $$
renice -n -5 -p $$  # negative = higher priority

# 4. Monitor CPU usage
top -b -n 1 | head -20
watch -n 1 'top -b -n 1 | head -20'

# 5. CPU-intensive task
for i in {1..4}; do sha256sum /dev/zero &; done
watch -n 1 'uptime; ps aux | grep sha256sum'

# 6. CPU affinity - pin to specific core
taskset -c 0 sha256sum /dev/zero &
taskset -c 1 sha256sum /dev/zero &
taskset -p -c 0 $$  # Set current shell to core 0

# 7. Check context switches
cat /proc/sched_debug | head -50

# 8. Profile syscall/context switches
strace -c sleep 1
dstat -s --top-latency 1

# 9. Monitor interrupts and context switches
cat /proc/stat
watch -n 1 'cat /proc/stat | head -2'

# 10. Performance profiling
perf stat sleep 1
perf record -g -- sleep 1
perf report
```

## Validation

Verify your knowledge:

```bash
# Can you check load average?
uptime && echo "✓ Load average visible"

# Can you change process priority?
nice -n 5 sleep 1 && echo "✓ Priority change works"

# Can you set CPU affinity?
taskset -c 0 echo "test" && echo "✓ CPU affinity works"

# Can you profile?
strace -c echo "test" && echo "✓ Profiling works"
```

## Cleanup

```bash
killall sha256sum sleep strace perf 2>/dev/null
```

## Common Mistakes

1. **Ignoring load average**: 4 on single core = bad, 4 on 32-core = fine
2. **CPU affinity without purpose**: Can reduce scheduler efficiency
3. **Setting nice too negative**: Might starve other processes
4. **Ignoring context switch cost**: High switches = performance loss
5. **Not profiling before optimizing**: Optimize bottlenecks, not guesses

## Troubleshooting

| Problem | Solution |
|---------|----------|
| High load average | Check: ps aux for CPU hogs, reduce workload |
| Slow performance | Profile: perf stat, identify bottleneck |
| High context switches | Check: thread count, lock contention |
| Uneven CPU usage | Use: taskset for symmetric load |
| Process starved | Adjust: nice priority, resource limits |

## Next Steps

1. Move to **11-distributed-systems-basics** for multi-machine scaling
2. Learn about real-time scheduling (SCHED_FIFO, SCHED_RR)
3. Study kernel tuning parameters
4. Explore advanced profiling (flame graphs, perf)
5. Learn about cgroups CPU limits

## Additional Resources

- Scheduler: `man 7 sched`
- Top: `man top`
- Nice: `man nice`
- Perf: `man perf`, https://www.brendangregg.com/flamegraphs.html

