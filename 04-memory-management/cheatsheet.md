# 04 - Memory Management Cheatsheet

## Memory Information Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `free -h` | Memory usage (human readable) | Shows: Mem, Swap, used, free, available |
| `free -m` | Memory in MB | More precise than -h |
| `cat /proc/meminfo` | Detailed memory info | All memory stats |
| `top -b -n 1` | One-time top snapshot | Shows memory and processes |
| `watch -n 1 'free -h'` | Monitor every 1 second | Live memory updates |

## Process Memory Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ps -o pid,vsz,rss,cmd` | Show VSZ and RSS | Virtual and resident memory |
| `ps aux --sort=-%mem` | Sort by memory usage | Highest memory first |
| `ps -p [pid] -o vsz,rss,cmd` | Single process | Specific PID only |
| `pmap -x [pid]` | Detailed memory map | Breakdown by region |
| `cat /proc/[pid]/status` | Process details | RSS, VSZ, Threads, etc |

## Memory Limits

| Command | Purpose | Example |
|---------|---------|---------|
| `ulimit -a` | Show all limits | List all resource limits |
| `ulimit -v` | Show virtual memory limit | Current limit |
| `ulimit -v [size]` | Set virtual memory limit | `ulimit -v 1000000` (1GB) |
| `ulimit -m` | Show physical memory limit | Resident memory |
| `ulimit -unlimited` | Remove limits | Reset to unlimited |

## Memory Metrics Explained

| Metric | Meaning | Notes |
|--------|---------|-------|
| Total | Sum of all RAM in system | Fixed value |
| Used | Allocated to processes + buffers | Can be freed |
| Free | Completely unused RAM | Can be allocated |
| Available | Can be freed if needed | Better than free |
| Buffers | Kernel buffer cache | Freed on demand |
| Cached | File system cache | Freed on demand |
| VSZ | Virtual memory allocated | Includes swap potential |
| RSS | Resident in physical RAM | True memory usage |
| PSS | Fair share (shared divided) | Accurate total |
| Swap | Pages on disk | Slower access |

## Memory Layout Regions

| Region | Purpose | Growth | Permissions |
|--------|---------|--------|-------------|
| Text | Executable code | None (fixed) | r-x (read-exec) |
| Initialized Data | Global variables | None | rw- (read-write) |
| BSS | Uninitialized globals | None | rw- |
| Heap | Dynamic allocation | Upward | rw- |
| Stack | Function calls, locals | Downward | rw- |

## Page Fault Information

| Type | Cause | Cost | Impact |
|------|-------|------|--------|
| Minor Fault | Page in RAM, TLB miss | ~10 cycles | Negligible |
| Major Fault | Page on disk, read | ~millions cycles | Bad performance |
| Access Fault | Permission denied | Exception | Segfault |

## Get Page Fault Stats

| Command | Purpose | Output |
|---------|---------|--------|
| `cat /proc/[pid]/stat` | Field 10 = minor, 12 = major | Numbers |
| `ps -o min_flt,maj_flt` | Show page faults | Columns |
| `time [command]` | Command timing | Reports major faults |

## Memory Optimization Tips

| Issue | Symptom | Solution |
|-------|---------|----------|
| High memory use | RSS >> VSZ unlikely | Check: large allocations, leaks |
| Memory leak | VSZ continuously growing | Monitor, find allocation loop |
| Swap usage | Slow performance | Increase RAM or reduce workload |
| Too many processes | OOM killer | Reduce process count |
| Fragmentation | Can't allocate large block | Restart processes |

## Common Memory Patterns

### Pattern 1: Memory Leak
```
Time:    T0      T1      T2
VSZ:    100MB  150MB  200MB  (growing!)
RSS:     50MB   75MB  100MB  (growing!)
```
Problem: Allocate without freeing

### Pattern 2: Memory Spike
```
Time:    T0      T1      T2
VSZ:    100MB  100MB  100MB  (stable)
RSS:     50MB  150MB   60MB  (spike then drops)
```
Problem: Temporary allocation released

### Pattern 3: Healthy Memory
```
Time:    T0      T1      T2
VSZ:    100MB  100MB  100MB  (stable)
RSS:     50MB   50MB   50MB  (stable)
```
Status: No leaks, normal operation

## Environmental Variables for Memory

| Variable | Value | Example |
|----------|-------|---------|
| POSIXLY_CORRECT | For standard behavior | Set when needed |
| VM parameters | In /proc/sys/vm/ | Kernel tuning |

## Swap Space Management

| Command | Purpose | Example |
|---------|---------|---------|
| `swapon -s` | Show swap devices | List all swap |
| `free -h` | Show swap usage | Column "Swap" |
| `swapoff /path` | Disable swap | Remove swap device |
| `swapon /path` | Enable swap | Add swap device |

## Memory Debugging Tools

| Tool | Purpose | Command |
|------|---------|---------|
| Valgrind | Memory errors | `valgrind ./program` |
| Gdb | Debugger | `gdb ./program` |
| Massif | Memory profiler | `valgrind --tool=massif` |
| AddressSanitizer | C/C++ memory bugs | Compile with `-fsanitize=address` |

## Quick Reference

```bash
# Check memory (most useful)
free -h

# Top 5 memory processes
ps aux --sort=-%mem | head -6

# Process memory detail
ps -o pid,vsz,rss,cmd -p [pid]

# Memory map
cat /proc/[pid]/maps

# Page faults
cat /proc/[pid]/stat | awk '{print $10, $12}'

# Set limit
ulimit -v 1000000
```
