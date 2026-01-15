# 10 - Scheduling and Performance Cheatsheet

## Load and Uptime Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `uptime` | Load average | 0.45, 0.50, 0.48 |
| `watch uptime` | Monitor load | Real-time updates |
| `cat /proc/loadavg` | Raw load data | Numbers only |
| `w` | Who and load | Users + load |
| `top` | Full monitoring | CPU, memory, processes |

## Priority (Nice) Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `nice -n [N] cmd` | Start with priority | nice -n 10 sleep 100 |
| `renice -n [N] -p [PID]` | Change priority | renice -n 5 -p 1234 |
| `ps -o nice,cmd` | Show priorities | Per-process nice |
| `ps -eo pid,ni,cmd` | Extended output | With nice column |

## CPU Affinity Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `taskset -c [CPU] cmd` | Pin to CPU | taskset -c 0 sleep 100 |
| `taskset -cp [PID]` | Check affinity | Which CPUs? |
| `taskset -cp [CPUs] [PID]` | Change affinity | taskset -cp 0,2 1234 |
| `numactl --physcpubind=0 cmd` | NUMA binding | taskset alternative |

## Process Monitoring Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `top` | Full monitoring | Real-time view |
| `ps -o pid,nice,cmd` | Process list | With nice |
| `ps -o pid,%cpu,%mem,cmd --sort=-%cpu` | By CPU usage | Sorted |
| `top -p [PID]` | Monitor one | Single process |

## Context Switch Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `cat /proc/[PID]/status` | Process details | Has ctxt_switches |
| `grep ctxt /proc/[PID]/status` | Context switches | Just that info |
| `cat /proc/stat` | CPU statistics | Global stats |
| `watch -n 1 'cat /proc/stat'` | Monitor stats | Real-time CPU |

## Nice Value Reference

| Nice | Priority | Use Case | Root |
|------|----------|----------|------|
| -20 | Highest | Critical services | Yes |
| -5 | High | Foreground apps | Yes |
| 0 | Default | Normal processes | No |
| 10 | Low | Background work | No |
| 19 | Lowest | Idle/batch | No |

## Load Average Interpretation

| Load | Meaning | Status |
|------|---------|--------|
| Load = CPUs | 1.0 = 1 CPU | Fully utilized |
| Load < CPUs | 0.5 = 1 CPU | Underutilized |
| Load > CPUs | 2.0 = 1 CPU | Overloaded |
| 3 numbers | 1, 5, 15 min | Trend analysis |

## CPU Info Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `nproc` | CPU count | 4, 8, 16... |
| `cat /proc/cpuinfo` | All CPU info | Detailed |
| `lscpu` | Human format | Clean output |
| `grep processor /proc/cpuinfo` | List processors | All cores |

## Performance Analysis

| Metric | Check | Command |
|--------|-------|---------|
| CPU usage | High % | top, ps |
| Load average | Trend | uptime |
| Context switches | Too many | /proc/stat |
| CPU affinity | Distribution | taskset -p |
| Memory pressure | High swap | free -h |

## Scheduler States

| State | Meaning | Duration |
|-------|---------|----------|
| Running | On CPU | ~10ms (quantum) |
| Ready | Waiting for CPU | Until scheduled |
| Blocked | I/O wait | Until event |
| Stopped | SIGSTOP signal | Until SIGCONT |
| Zombie | Terminated, awaiting | Until parent reaps |

## Performance Tuning Tips

```bash
# Check load
uptime

# Find CPU hogs
top -b -n 1 | head -20

# Check CPU distribution
top -p and press 1

# Pin process to CPU
taskset -c 0 command

# Change priority
renice -n 10 -p [PID]

# Monitor context switches
watch 'grep ctxt /proc/[PID]/status'
```

## Thread vs Process Scheduling

| Factor | Threads | Processes |
|--------|---------|-----------|
| Kernel aware | Yes | Yes |
| Scheduler | Per-thread | Per-process (CFS) |
| Context switch | Light | Heavy |
| Cache benefit | Shared L2/L3 | Separate |
| Affinity | Per-thread | Per-process |

## Real-Time Scheduling

| Class | Range | Use Case |
|-------|-------|----------|
| Normal | nice -20 to 19 | Regular processes |
| Real-time | priority 0-99 | Kernel only |
| Batch | Nice 19 | Background |
| Idle | Special | Very low priority |

## Quick Performance Check

```bash
# Overall load
uptime

# Per-CPU
top (press 1)

# Process CPU time
ps -eo pid,%cpu,cmd --sort=-%cpu

# Context switches
grep ctxt /proc/[PID]/status

# Memory usage
free -h

# Disk I/O
iostat 1 5
```

## Common Performance Issues

| Problem | Cause | Solution |
|---------|-------|----------|
| High load | CPU saturation | Renice, add CPU |
| Low CPU usage | I/O wait | Check disk/network |
| Many context switches | Too many threads | Reduce parallelism |
| Cache misses | Poor affinity | Use taskset |
| Unbalanced load | Pinned processes | Rebalance CPUs |
