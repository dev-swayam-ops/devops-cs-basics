# 04 - Memory Management

## What You'll Learn

Master memory management concepts:
- Memory layout and segments (stack, heap, BSS)
- Virtual vs physical memory
- Page faults and paging
- Memory allocation and deallocation
- Memory leaks and debugging
- Performance optimization
- Memory limits and ulimit

## Prerequisites

- Completed **03-threads-and-concurrency**
- Understanding of processes and memory basics
- Basic understanding of addresses

## Key Concepts

### 1. Memory Layout
```
High Address  [Command Line & Environment Variables]
              [Stack] (grows downward)
              [Unused Memory]
              [Heap] (grows upward)
              [BSS Segment] (uninitialized data)
              [Data Segment] (initialized data)
Low Address   [Text Segment] (code)
```

### 2. Virtual vs Physical Memory
- **Virtual Memory**: Process's view of memory (linear, starting at 0)
- **Physical Memory**: Actual RAM
- **Paging**: OS swaps pages to disk to extend RAM
- **TLB**: Translation Lookaside Buffer (cache for address translation)

### 3. Memory Metrics
- **VSZ** (Virtual Set Size): Total virtual memory allocated
- **RSS** (Resident Set Size): Physical memory currently in RAM
- **PSS** (Proportional Set Size): Shared memory divided fairly

### 4. Page Faults
- **Minor Fault**: Page in RAM, just need to map it
- **Major Fault**: Page on disk, need to read from disk (slow!)

## Hands-on Lab: Memory Analysis

### Lab Steps

```bash
# 1. Check system memory
free -h
free -m

# Expected output:
#                total        used        free      shared  buff/cache   available
# Mem:           15Gi       4.2Gi       2.3Gi       256Mi       8.5Gi       10Gi
# Swap:          2.0Gi          0B       2.0Gi

# 2. View memory details
cat /proc/meminfo

# 3. Check process memory usage
ps aux
ps -o pid,vsz,rss,cmd

# 4. Monitor specific process
watch -n 1 'ps -p $$ -o pid,vsz,rss,cmd'

# 5. Allocate memory dynamically
bash -c 'a=($(seq 1 1000000)); sleep 10'

# 6. Check memory in another terminal
ps aux | grep bash

# 7. View process memory map
cat /proc/self/maps

# Expected output:
# 56148d2a2000-56148d2ac000 r-xp 00000000 08:01 1234567 /bin/bash
# 56148d4ab000-56148d4c5000 rw-p 00009000 08:01 1234567 /bin/bash
# 7f3a2e000000-7f3a2e1d1000 rw-p 00000000 00:00 0       [heap]

# 8. Check page fault statistics
cat /proc/self/stat | awk '{print "Minor faults: " $10 ", Major faults: " $12}'

# 9. Set memory limits
ulimit -a
ulimit -v 1000000  # Limit virtual memory to 1GB
bash
  ps aux | grep bash
ulimit -v unlimited  # Remove limit

# 10. Detect memory leaks
valgrind --leak-check=full ./program  # For compiled programs
```

## Validation

Verify your memory knowledge:

```bash
# Can you check free memory?
free -h && echo "✓ Memory check works"

# Can you understand VSZ vs RSS?
ps -o pid,vsz,rss,cmd | head && echo "✓ Memory metrics visible"

# Can you set limits?
ulimit -v 1000000 && echo "✓ Can set limits"

# Can you view memory maps?
cat /proc/$$/maps | head && echo "✓ Can view maps"
```

## Cleanup

```bash
# Remove any memory limits
ulimit -v unlimited
ulimit -s unlimited

# Kill processes
killall sleep 2>/dev/null
```

## Common Mistakes

1. **Confusing VSZ and RSS**: VSZ is allocated, RSS is actually in RAM
2. **Ignoring swap**: Swap is on disk, much slower than RAM
3. **Memory limits**: ulimit -v includes swap, not just RAM
4. **Page fault cost**: Major faults (disk) are expensive, minor faults are cheap
5. **Shared memory**: RSS counts shared memory per process (use PSS for accurate total)

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Out of memory | Check: top, kill large processes, add swap |
| Memory leak | Use: valgrind, instruments, check allocations |
| Slow performance | Check: page faults (cat /proc/*/stat), swap usage |
| Process killed | Check: OOM killer, memory limits |
| High swap usage | Reduce: process count, increase RAM |

## Next Steps

1. Move to **05-storage-and-filesystems** for disk I/O
2. Learn about garbage collection
3. Study memory optimization techniques
4. Explore cgroups memory limits
5. Learn about memory pressure and OOM killer

## Additional Resources

- Memory layout: `man 7 memory`
- Process memory: `cat /proc/[pid]/maps`
- Memory statistics: `man free`, `man top`
- Valgrind: https://www.valgrind.org/

