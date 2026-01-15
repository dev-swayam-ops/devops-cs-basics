# 04 - Memory Management

## What You'll Learn

- Memory layout (stack, heap, BSS, data, text)
- Virtual vs physical memory
- Page faults and paging
- Memory allocation and deallocation
- Memory metrics (VSZ, RSS, PSS)
- Performance optimization
- Memory limits and resource management

## Prerequisites

- Completed **03-threads-and-concurrency**
- Understanding of memory basics
- Process and memory relationship

## Key Concepts

### 1. Memory Layout
```
High Address: [Stack] (grows down, function calls)
              [Heap] (grows up, dynamic allocation)
              [BSS] (uninitialized globals)
Low Address:  [Data] (initialized globals)
              [Text] (code, read-only)
```

### 2. Virtual vs Physical Memory
- **Virtual Memory**: Process's view (linear address space from 0)
- **Physical Memory**: Actual RAM on system
- **Paging**: OS swaps pages to disk when RAM full
- **TLB**: Translation Lookaside Buffer (cache of address translations)

### 3. Memory Metrics
- **VSZ** (Virtual Set Size): Total virtual memory allocated
- **RSS** (Resident Set Size): Physical memory in RAM (non-shared)
- **PSS** (Proportional Set Size): Fair share of shared memory
- **Swap**: Pages on disk (slow access)

### 4. Page Faults
- **Minor Fault**: Page in RAM, just need mapping
- **Major Fault**: Page on disk, read from disk (slow)
- Measure with: `cat /proc/[pid]/stat` (field 10, 12)
- More major faults = worse performance

## Hands-on Lab: Memory Exploration

### Lab Overview
Analyze memory usage and understand memory metrics.

### Lab Commands

```bash
# 1. Check total system memory
free -h

# Expected output:
#              total used free
# Mem:         15Gi  4Gi  11Gi
# Swap:        2.0Gi  0B  2.0Gi

# 2. Show detailed memory
cat /proc/meminfo | head -10

# Expected: MemTotal, MemFree, MemAvailable, Buffers, Cached

# 3. Check process memory (current shell)
ps -o pid,vsz,rss,cmd -p $$

# Expected:
# PID  VSZ     RSS    CMD
# 2345 4234    2340   bash

# 4. Calculate memory percentage
free | awk 'NR==2 {printf "Used: %.1f%%\n", ($3/$2)*100}'

# Expected: Used: 26.7%

# 5. Monitor memory in real-time
watch -n 1 'free -h'

# (Refresh every 1 second, Press q to exit)

# 6. View page fault statistics
cat /proc/$$/stat | awk '{print "Minor: " $10 ", Major: " $12}'

# Expected: Minor: 1234, Major: 5

# 7. Allocate memory dynamically
bash -c 'a=($(seq 1 1000000)); sleep 5'

# Check memory in another terminal with ps

# 8. Check memory of all processes
ps aux --sort=-%mem | head -5

# Expected: Top 5 by memory usage

# 9. Set memory limit
ulimit -v 1000000  # Limit to ~1GB virtual

# Try to allocate: bash
# bash -c 'a=($(seq 1 10000000))'  # Will fail

# 10. Show memory map
cat /proc/self/maps | head -5

# Expected: Memory regions with permissions
```

## Validation

Verify your understanding:

```bash
# Can you check free memory?
free -h && echo "✓ Memory check works"

# Understand VSZ vs RSS?
ps -o pid,vsz,rss,cmd | head && echo "✓ Memory metrics visible"

# Can you set memory limits?
ulimit -v && echo "✓ Limits accessible"

# Know page fault concept?
echo "✓ Page fault concept understood"
```

## Cleanup

```bash
# Remove limits
ulimit -v unlimited

# Kill processes
killall bash 2>/dev/null
```

## Common Mistakes

1. **Confusing VSZ and RSS**: VSZ allocated, RSS actually in RAM
2. **Ignoring swap**: Swap is disk-based (much slower)
3. **Memory limits**: ulimit -v includes swap
4. **Page fault cost**: Major faults expensive, minor faults cheap
5. **Shared memory**: RSS counts per-process, use PSS for total

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Out of memory | Check: top, kill large processes, add swap |
| Memory leak | Monitor: VSZ growth over time |
| Slow performance | Check: page faults, swap usage |
| Process killed (OOM) | Check: kernel logs, increase limits |
| High swap usage | Add RAM or reduce workload |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for reference
4. Move to **05-storage-and-filesystems** after completion

## Additional Resources

- Memory: `man 7 memory`
- Process memory: `/proc/[pid]/` documentation
- ulimit: `man bash` (look for ulimit)
- Memory tools: `man top`, `man free`, `man ps`

