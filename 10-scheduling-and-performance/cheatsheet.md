# 10 - Scheduling and Performance: Cheatsheet

## CPU Scheduling

| Concept | Definition | Impact |
|---------|-----------|--------|
| **Context Switch** | OS switches CPU between processes | 1-10 microseconds overhead |
| **Time Slice/Quantum** | Time each process runs | Typically 10-100ms |
| **Preemption** | OS interrupts running process | Ensures fairness |
| **Load Average** | Average # of runnable processes | Indicator of CPU saturation |
| **Nice Value** | Process priority (-20 to +19) | Lower = higher priority |
| **Real-Time** | Highest priority processes | For latency-sensitive tasks |

## Scheduling Algorithms

```
FIFO (First In First Out):
- Simple but unfair
- One long process blocks others

Round Robin:
- Each process gets time slice
- Fair, good response time

Priority-Based:
- Higher priority runs first
- Can starve low-priority

Multi-Level Queue:
- Different queues for different priorities
- Linux standard approach
```

## Performance Monitoring

```bash
uptime                 # Load average
top                    # CPU usage, context switches
vmstat 1 5            # Page faults, CPU time
iostat                # Disk I/O performance
mpstat                # Per-CPU statistics
perf top              # CPU hotspots
```

## Performance Tuning

```bash
# View nice value
ps -l | grep process

# Set nice value at start
nice -n 10 ./program

# Change running process
renice -n 5 -p [PID]

# Real-time priority
chrt -p [PID]
sudo chrt -f -p 99 [PID]  # FIFO, highest priority

# CPU affinity (pin to CPU)
taskset -c 0 ./program    # Run on CPU 0
```

## Key Metrics

```
CPU Utilization: % time CPU is busy (target: 60-80%)
Context Switches: # per second (high = thrashing)
Load Average: Running processes (1-2x CPU count OK)
Wait Time: Time waiting for CPU
Throughput: Tasks completed per second
Latency: Time from request to response
```

---
