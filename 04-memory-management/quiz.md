# 04 - Memory Management: Quiz

## Multiple Choice Questions (7 Questions)

### Question 1
What is the main difference between VSZ and RSS?

A) VSZ is total memory, RSS is shared memory  
B) VSZ is virtual memory allocated, RSS is actual RAM being used  
C) VSZ is faster than RSS  
D) They are the same thing

<details>
<summary>Answer</summary>
<strong>B) VSZ is virtual memory allocated, RSS is actual RAM being used</strong>

VSZ (Virtual Size) includes all allocated memory pages that might be on disk. RSS (Resident Set Size) only counts pages actually in physical RAM. RSS is the better metric for real memory usage.
</details>

---

### Question 2
Which memory segment grows downward (toward lower addresses)?

A) Heap  
B) Stack  
C) Data segment  
D) Text segment

<details>
<summary>Answer</summary>
<strong>B) Stack</strong>

The stack grows downward (decreasing addresses) as functions are called and local variables are pushed. The heap grows upward (increasing addresses) as memory is allocated.
</details>

---

### Question 3
What is a page fault?

A) An error in the page file  
B) When a process accesses memory that isn't currently mapped in RAM  
C) When the hard disk fails  
D) A networking error

<details>
<summary>Answer</summary>
<strong>B) When a process accesses memory that isn't currently mapped in RAM</strong>

A page fault occurs when a process accesses a virtual memory address whose page isn't in RAM. The OS then loads it from disk (major fault, slow) or allocates a new page (minor fault, fast).
</details>

---

### Question 4
What does a memory limit (ulimit -v) prevent?

A) The kernel from running  
B) A process from using more than the allocated virtual memory  
C) Swap from being used  
D) The page cache from working

<details>
<summary>Answer</summary>
<strong>B) A process from using more than the allocated virtual memory</strong>

`ulimit -v` sets a ceiling on virtual memory. When a process tries to exceed it, it gets SIGSEGV (segmentation fault), preventing runaway memory allocation.
</details>

---

### Question 5
What is a memory leak?

A) Using swap space  
B) Memory allocated but never freed, gradually consuming all RAM  
C) Cache memory failing  
D) Accessing invalid memory

<details>
<summary>Answer</summary>
<strong>B) Memory allocated but never freed, gradually consuming all RAM</strong>

A memory leak occurs when allocated memory is lost (no references to it remain) and can't be freed. Over time, this consumes all available memory and crashes the program.
</details>

---

### Question 6
Why does sequential memory access typically perform better than random access?

A) Sequential is always smaller  
B) CPU caches prefetch sequential locations; random breaks cache locality  
C) Sequential doesn't use memory  
D) They perform identically

<details>
<summary>Answer</summary>
<strong>B) CPU caches prefetch sequential locations; random breaks cache locality</strong>

CPUs have caches that predict forward sequential access and prefetch lines. Random access defeats this optimization, causing many cache misses and forcing reads from slow main memory.
</details>

---

### Question 7
What happens if you use too much swap space?

A) System automatically optimizes  
B) System performance degrades dramatically  
C) Memory is freed automatically  
D) Swap is disabled

<details>
<summary>Answer</summary>
<strong>B) System performance degrades dramatically</strong>

Disk access is ~1000x slower than RAM. High swap usage means constant disk I/O (thrashing), causing severe performance degradation. It's a symptom of insufficient physical RAM.
</details>

---

## Short Answer Questions (3 Questions)

### Question 8
You notice your application's RSS grows from 100MB to 800MB over 24 hours, but the application doesn't use more features. What's likely happening, and how would you investigate?

<details>
<summary>Answer</summary>

**Most likely**: Memory leak

**Investigation steps:**
1. Monitor RSS trend:
   ```bash
   watch -n 60 'cat /proc/[PID]/status | grep VmRSS'
   ```
   If continuously increasing → definite leak

2. Identify where memory is allocated:
   ```bash
   # Using strace (shows allocation calls)
   strace -e trace=mmap,brk ./app 2>&1 | tail -100
   ```

3. Use memory profiler:
   ```bash
   python -m memory_profiler app.py
   # Shows which functions allocate most memory
   ```

4. Check for common leaks:
   - Unclosed file handles: `lsof -p [PID]`
   - Accumulated data structures
   - Event listeners not removed
   - Circular references

**Fix approach:**
- Add explicit memory cleanup
- Implement garbage collection
- Profile and optimize allocation patterns
- Set memory limits as safety net
</details>

---

### Question 9
Explain why mmap-ing a large file for read-only access is more memory-efficient than reading it into a buffer, even though both end up in the page cache.

<details>
<summary>Answer</summary>

**Traditional Read:**
```bash
# Allocate buffer and read
buffer = malloc(file_size)
read(fd, buffer, file_size)
# Buffer allocated in heap
# Data copied from kernel to user space
# Memory: file_size + overhead
```

**Memory-mapped:**
```bash
# Map file into address space
ptr = mmap(fd, file_size)
# Accesses go directly through page cache
# OS handles paging automatically
```

**Why mmap is more efficient:**
1. **No explicit buffer**: Don't allocate separate user buffer
2. **Single copy**: Kernel page cache serves both kernel and user
3. **Lazy loading**: Only read pages actually accessed (sparse reading)
4. **Automatic paging**: OS handles cache management
5. **Address space sharing**: Multiple processes can share same cached pages

**Example efficiency:**
- File: 1GB, need only first 10MB
- Traditional read: 1GB RAM used
- mmap: ~10MB RAM used (only accessed pages)

**Trade-off:**
- Overhead: Page faults, TLB misses
- Benefit: Efficient memory usage, especially for large files
- Best for: Read-only, random/sparse access to large files
</details>

---

### Question 10
Design a monitoring system that alerts a DevOps engineer when memory conditions are becoming critical. What metrics would you monitor, and what thresholds would trigger alerts?

<details>
<summary>Answer</summary>

**Key Metrics to Monitor:**

```bash
#!/bin/bash

check_memory_health() {
    # Parse memory info
    meminfo=$(cat /proc/meminfo)
    mem_total=$(echo "$meminfo" | grep MemTotal | awk '{print $2}')
    mem_available=$(echo "$meminfo" | grep MemAvailable | awk '{print $2}')
    swap_total=$(echo "$meminfo" | grep SwapTotal | awk '{print $2}')
    swap_used=$(echo "$meminfo" | grep SwapCached | awk '{print $2}')
    
    # Calculate percentages
    mem_used=$((mem_total - mem_available))
    mem_pct=$((mem_used * 100 / mem_total))
    swap_pct=$((swap_used * 100 / swap_total))
    
    # Define thresholds
    CRITICAL_MEM=90
    WARNING_MEM=80
    CRITICAL_SWAP=80
    WARNING_SWAP=50
    
    # Check memory
    if [ $mem_pct -gt $CRITICAL_MEM ]; then
        alert "CRITICAL" "Memory: ${mem_pct}%"
    elif [ $mem_pct -gt $WARNING_MEM ]; then
        alert "WARNING" "Memory: ${mem_pct}%"
    fi
    
    # Check swap
    if [ $swap_pct -gt $CRITICAL_SWAP ]; then
        alert "CRITICAL" "Swap: ${swap_pct}% (performance degrading)"
    elif [ $swap_pct -gt $WARNING_SWAP ]; then
        alert "WARNING" "Swap: ${swap_pct}% (approaching critical)"
    fi
}
```

**Recommended Thresholds:**

| Metric | Green | Yellow | Red |
|--------|-------|--------|-----|
| Memory Used % | <70% | 70-85% | >85% |
| Swap Used % | <20% | 20-50% | >50% |
| Available Memory | >20% | 10-20% | <10% |
| Page In/Out Rate | <1000 | 1000-10000 | >10000 |
| OOM Killer Events | 0 | Alert | Alert |

**Monitoring Implementation:**

```bash
#!/bin/bash

ALERT_EMAIL="devops@company.com"
ALERT_WEBHOOK="https://alerts.company.com/webhook"

alert() {
    local level=$1
    local message=$2
    local timestamp=$(date)
    
    # Log locally
    echo "[$timestamp] $level: $message" >> /var/log/memory_monitoring.log
    
    # Send to monitoring system
    if [ "$level" = "CRITICAL" ]; then
        curl -X POST $ALERT_WEBHOOK \
          -H "Content-Type: application/json" \
          -d "{\"level\":\"$level\",\"message\":\"$message\",\"host\":\"$(hostname)\"}"
        
        # Send email for critical
        echo "$message" | mail -s "CRITICAL Memory Alert" $ALERT_EMAIL
    fi
}

# Run continuously
while true; do
    check_memory_health
    sleep 60  # Check every minute
done
```

**Advanced Monitoring Features:**

```bash
# 1. Track per-process memory usage
ps aux --sort=-%mem | head -10 >> /var/log/top_memory_processes.log

# 2. Monitor memory growth rate
CURRENT_MEM=$(free | grep Mem | awk '{print $3}')
if [ -f /tmp/prev_mem ]; then
    PREV_MEM=$(cat /tmp/prev_mem)
    GROWTH=$((CURRENT_MEM - PREV_MEM))
    if [ $GROWTH -gt 1000000 ]; then  # >1GB growth in 5 minutes
        alert "WARNING" "Memory growing rapidly: +${GROWTH}KB"
    fi
fi
echo $CURRENT_MEM > /tmp/prev_mem

# 3. Detect OOM killer activity
OOM_COUNT=$(grep -c "Out of memory" /var/log/kern.log)
if [ $OOM_COUNT -gt 0 ]; then
    alert "CRITICAL" "OOM killer triggered $OOM_COUNT times"
fi

# 4. Monitor cache effectiveness
cat /proc/vmstat | grep -E "pswpin|pswpout" >> memory_stats.log
```

**Integration with Observability Stack:**
- Send metrics to Prometheus
- Create Grafana dashboards
- Set up PagerDuty escalation
- Correlate with application logs
</details>

---

## Answer Key Summary

| Question | Answer |
|----------|--------|
| 1 | B |
| 2 | B |
| 3 | B |
| 4 | B |
| 5 | B |
| 6 | B |
| 7 | B |
| 8 | See detailed answer above |
| 9 | See detailed answer above |
| 10 | See detailed answer above |

---

## Scoring Guide

- **9-10 correct**: Expert understanding of memory management and diagnostics
- **7-8 correct**: Strong knowledge, ready for performance optimization work
- **5-6 correct**: Solid fundamentals, review page faults and memory leaks
- **Below 5**: Study memory layout, VSZ/RSS, and debugging techniques

---
