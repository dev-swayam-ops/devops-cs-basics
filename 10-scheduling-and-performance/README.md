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

The scheduler is the kernel component that decides which ready process gets to run on each CPU. It's one of the most important parts of the kernel for system performance.

**Scheduling Goal**
- Fair allocation of CPU time
- Every process gets reasonable CPU time
- Responsive system - interactive programs react quickly
- Prevent starvation - no process should wait indefinitely
- Good throughput - maximize useful work done
- Balance between fairness and responsiveness

**Time Slices (Quantum)**
- Each process given time slice of CPU: ~1-100ms depending on scheduler
- CFS (Completely Fair Scheduler - modern Linux): dynamic based on load
- Timer interrupt fires when time slice expires
- Scheduler preempts process (forcibly pauses) and selects next runnable process
- After time slice: process moved back to ready queue
- Too large: unresponsive, low interactivity
- Too small: excessive context switching, high overhead

**Ready Queue**
- Kernel maintains queue of processes ready to run
- Ordered by priority and fair CPU time received
- Scheduler picks from front of queue
- Only runnable processes in queue - blocked processes elsewhere
- Size: typically hundreds to thousands on busy system

**Runnable State**
- Process ready to run but waiting for CPU
- In ready queue
- Will run as soon as scheduled
- Different from Blocked (waiting for I/O)
- Many processes can be runnable simultaneously

**Preemption**
- Scheduler forcibly pauses process after time slice
- Process doesn't volunteer to stop - it's forced
- Ensures fair time allocation
- Without preemption: one process could monopolize CPU
- Preemption requires timer interrupt

**Multi-Core Scheduling**
- Each CPU core has own ready queue
- Scheduler tries to load-balance: spread processes across cores
- Process doesn't stick to one core - can migrate between cores
- But process more efficient if stays on same core (cache locality)
- Kernel tries to keep process on same core if possible

### 2. Process Priority (Nice and Real-Time)

Priority determines which process gets CPU time when multiple runnable processes exist. Higher priority processes run more frequently.

**Nice Value (User Priority)**
- Range: -20 (highest, most CPU) to 19 (lowest, least CPU)
- Default: 0
- Lower number = higher priority = gets more CPU time
- Nice increment: each level is ~10% difference in CPU allocation
- Changed with `nice` (at startup) or `renice` (running process)
- Only root can lower nice (increase priority)
- Regular users can only raise nice (lower priority)

**Nice Semantics**
- "Nice" value: how nice (polite) process is to others
- Nice -20: selfish - takes maximum CPU
- Nice 19: very nice - yields CPU to others
- Default 0: neutral priority
- Interactive tasks (editor): low nice to be responsive
- Batch jobs (compilation): high nice to not interfere

**Real-Time Priority (Kernel Priority)**
- Range: -100 to -51 (negative numbers, kernel reserved)
- Completely different from nice: processes with real-time priority preempt normal processes
- Real-time process always runs before normal priority process
- Used for: audio processing, control systems, latency-critical code
- Requires root to use
- Too many real-time processes can starve normal processes

**Real-Time Scheduling Classes**
- SCHED_FIFO: First-in-First-out, runs until blocks or yields
- SCHED_RR: Round-robin, same as FIFO but time slice enforced
- SCHED_DEADLINE: Deadline scheduling, tasks with deadlines

**Setting Priority**
```bash
nice -n 10 command          # Start with nice=10
renice -n 5 -p PID        # Change running process to nice=5
```

**Priority Inheritance**
- Child inherits parent's priority at creation
- Can be changed afterwards
- Batch child of interactive parent will have normal priority

### 3. Load Average and System Load

Load average is a single number that tells you how busy the system is. It's essential for capacity planning and monitoring.

**What Load Average Means**
- Number of processes ready to run (in run queue)
- Average over time window
- Load 1.0 = 1 process on CPU, none waiting
- Load 2.0 = 1 on CPU, 1 waiting (or CPU overloaded depending on interpretation)

**Interpreting Load with CPU Count**
- Load relative to CPU count
- Single CPU system: Load 1.0 = fully utilized, 2.0 = overloaded
- Quad-core system: Load 4.0 = fully utilized, 8.0 = overloaded
- Rule of thumb: Load > CPU count = system overloaded
- Load < CPU count = system has idle capacity

**Three Load Averages**
- uptime shows three numbers: 1-min, 5-min, 15-min
- Example: `load average: 0.25, 0.30, 0.35`
- 1-min: last minute load
- 5-min: average over 5 minutes (smoothed)
- 15-min: average over 15 minutes (long-term trend)
- Useful: compare to see if load increasing or decreasing

**Load Average Calculation**
- Kernel samples ready queue periodically (every few seconds)
- Exponential moving average: newer samples weighted higher
- Low values: system responsive
- High values: system busy, responses slow
- Very high: possible thrashing (excessive context switching)

**Load vs CPU Usage**
- Different metrics
- Load: number of runnable processes
- CPU usage: percentage of CPU time spent working (not idle)
- High load can mean: waiting for I/O (not using CPU)
- Low CPU usage with high load: processes blocked on I/O

**Ideal Load**
- For throughput: Load = number of CPU cores
- For responsiveness: Load < number of CPU cores
- Too low: CPU idle, not using available resources
- Too high: excessive waiting, thrashing

**Monitoring Load**
```bash
uptime                    # Show current load
watch -n 1 'uptime'      # Monitor load every second
cat /proc/loadavg         # Raw load data
```

### 4. Context Switching

Context switch is when the CPU stops running one process and starts running another. It's a necessary but expensive operation.

**What Happens During Context Switch**
1. Timer interrupt fires (end of time slice)
2. Kernel saves CPU state of current process: registers, program counter, flags
3. Kernel restores CPU state of next process: different registers, different code location
4. CPU resumes execution of next process from where it was paused
5. TLB (translation lookaside buffer) flushed - address translations invalid
6. CPU caches may be invalidated - new process has different memory access pattern

**Context Switch Overhead**
- 1000-10000 CPU cycles per switch
- On modern CPUs: 1-10 microseconds
- Seems fast but adds up with frequent switches
- Main cost: cache misses and TLB flush
- L3 cache: slow to rebuild (100s of millions of cycles to refill)
- Opportunity cost: CPU could have been running user code

**Voluntary vs Involuntary Switches**
- Voluntary: process calls sleep(), blocks on I/O, yields
- Involuntary: timer interrupt preempts process
- Both have same cost
- Voluntary more efficient if waiting for slow I/O (saves CPU time)

**Too Many Context Switches (Thrashing)**
- If processes constantly switching: system spends time switching, not working
- Symptoms: high load but low throughput
- Causes: too many processes for available CPU, very small time slices
- Solution: reduce number of runnable processes, increase time slice
- Extreme: system so busy switching that nothing gets done

**Measuring Context Switches**
```bash
cat /proc/stat                          # Total switches
cat /proc/[pid]/status | grep ctxt      # Per-process switches
ps -o pid,csw,cmd                       # Per-process
```

**Optimization**
- Reduce number of processes: fewer context switches needed
- Single-threaded with async I/O: one process, no switching within it
- Process affinity: keep process on same CPU (reduce cache misses)
- Batch related work: process same data before switching

### 5. CPU Affinity (CPU Pinning)

CPU affinity allows binding a process to specific CPU cores, improving performance by reducing cache misses and context switches.

**What Is CPU Affinity**
- Restricting process to run on specific CPU core(s)
- By default: process can run on any available core
- Affinity: "keep this process on CPU 0 and 1"
- Kernel respects affinity when scheduling

**Why CPU Affinity Helps**
- **Cache locality**: L1/L2 cache is per-core, L3 shared
- If process stays on same core: cache memory likely still there
- If process bounces between cores: cache misses, fetches from RAM
- RAM access ~100x slower than L1 cache
- Process affinity to same core: 10-20% performance improvement

**Use Cases**
- Database threads: pin to specific core for cache efficiency
- Real-time systems: pin to dedicated core, prevent interference
- NUMA systems: pin to cores on same memory node
- Latency-critical: prevent scheduler from moving between cores
- Testing: reproducible performance measurements

**CPU Sets and NUMA**
- NUMA (Non-Uniform Memory Access): memory latency varies by location
- Better to keep process on cores close to its memory
- CPU sets: group cores and memory together
- Kernel knows topology: tries to schedule on local cores
- Manual affinity: override kernel decisions for critical tasks

**Setting CPU Affinity**
```bash
taskset -c 0,1 command          # Run on cores 0 and 1
taskset -c 0 sleep 100 &        # Pin sleep to core 0
taskset -pc 0 $PID              # Change running process affinity
```

**Viewing CPU Affinity**
```bash
taskset -pc $PID                # Show current affinity
cat /proc/$PID/cpuset          # Cpuset assignment
```

**Affinity Inheritance**
- Child processes inherit parent's affinity
- Can be overridden by child
- Useful: set container CPU affinity, children inherit

**Trade-offs**
- Affinity reduces CPU mobility and flexibility
- Oversubscribing (more runnable than cores): pinned processes might starve
- Overuse: can reduce fairness and responsiveness
- Best for: dedicated servers, latency-critical, NUMA optimization

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

