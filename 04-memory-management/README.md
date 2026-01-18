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

A process's memory is divided into segments: **Text** (code), **Data** (initialized globals), **BSS** (uninitialized globals), **Heap** (dynamic allocation), **Stack** (function calls & locals).

**Text Segment**
- Contains executable code (read-only)
- Lowest addresses in memory

**Data + BSS Segments**
- **Data**: Global/static variables with explicit initialization
- **BSS**: Global/static variables without initialization (auto-zeroed by kernel)

**Heap**
- Dynamic memory from `malloc/calloc`
- Grows upward; needs `free()` to avoid leaks

**Stack**
- Local variables and function parameters
- Grows downward; auto-cleaned when scope ends
- Each thread gets its own stack

**Memory Layout** (typical address order):
```
High Address  [ Stack ] ↓ (grows down)
              [ Heap ]  ↑ (grows up)
              [ BSS ]
              [ Data ]
Low Address   [ Text ]
```

**DevOps Relevance**: Understand where memory issues come from—heap leaks, stack overflows, or runaway globals.

### 2. Virtual vs Physical Memory

**Virtual Memory**: Each process sees its own private address space (0x0 → 2^64). Kernel maps virtual addresses to physical RAM using page tables.

**Physical Memory**: Actual RAM—limited resource, shared by all processes.

**Paging**: When RAM is full, kernel swaps least-used pages to disk. Accessing disk is 1000× slower than RAM.

**TLB (Translation Lookaside Buffer)**: Hardware cache for virtual→physical address translations. On context switch, TLB is flushed (causes performance hit).

**DevOps Relevance**: High swap usage = memory pressure. Monitor with `free`, `vmstat`, or `/proc/meminfo`. Tune kernel swappiness to control aggressiveness.

### 3. Memory Metrics

**VSZ (Virtual Set Size)**: Total virtual memory allocated. Often much larger than actual RAM used (includes swap + libraries). Not useful for understanding memory pressure.

**RSS (Resident Set Size)**: Physical RAM actually in use now. Useful metric for process memory consumption. Caveat: counts full page even if shared.

**PSS (Proportional Set Size)**: Fair share of shared memory. Most accurate for total system memory cost. Example: 10 processes sharing 1MB library = 100KB each in PSS.

**Swap**: Memory written to disk when RAM is full. High swap = memory pressure (slow!).

**DevOps Relevance**: Monitor RSS for process memory; use PSS to understand true system load. Watch swap to detect memory pressure.

### 4. Page Faults

**Page Fault**: Process accesses memory not currently in RAM. Kernel must handle (slow!).

**Minor Fault**: Page exists in RAM but mapping needs setup. Very fast (microseconds). Normal at process startup.

**Major Fault**: Page on disk (swapped)—must read from disk. Slow! Indicates memory pressure.

**Monitor with**:
```bash
ps -o pid,min_flt,maj_flt,cmd  # Check faults per process
vmstat 1                        # Watch page faults in real-time
```

**DevOps Action**: Consistent major faults = add RAM or reduce load. Thousands/sec = severe memory pressure.

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

