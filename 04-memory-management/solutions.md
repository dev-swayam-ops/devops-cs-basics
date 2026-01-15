# 04 - Memory Management Solutions

## Easy Solutions (1-5)

### Solution 1: Check System Memory Status

**Command**:
```bash
free -h
```

**Output Example**:
```
              total  used  free  shared  buff/cache  available
Mem:          15Gi  4.2Gi 2.3Gi  256Mi       8.5Gi       10Gi
Swap:         2.0Gi    0B  2.0Gi
```

**Answers**:
- Used percentage: 4.2 / 15 = 28%
- Swap used: 0B (not needed)
- Free: 2.3Gi (completely unused)
- Available: 10Gi (can be freed if needed - includes cache)

**Explanation**:
- `available` > `free` (cache can be freed)
- Swap unused = memory pressure not critical
- 28% used = healthy system

---

### Solution 2: View Process Memory Usage

**Commands**:
```bash
echo $$
ps -o pid,vsz,rss,cmd -p $$
```

**Output Example**:
```
2345
PID  VSZ     RSS    CMD
2345 4234    2340   bash
```

**Answers**:
- VSZ: 4234 KB = ~4.2 MB (virtual memory)
- RSS: 2340 KB = ~2.3 MB (physical RAM)
- Ratio: 2340/4234 = 55% (about half in RAM)

**Explanation**:
- VSZ = total allocated (includes shared libs, swap potential)
- RSS = actually in RAM (dirty pages)
- Difference = can be swapped to disk

---

### Solution 3: Compare Memory Before and After

**Commands**:
```bash
# Before
free -h

# Open large app (e.g., Firefox)
firefox &

# After
free -h
```

**Output Example**:
```
Before: Mem: 15Gi used 4.2Gi free 10Gi
After:  Mem: 15Gi used 6.5Gi free 8Gi
```

**Analysis**:
- Used increased by 2.3Gi
- Available decreased by 2.3Gi
- Free may not change (used includes cache)
- Application took ~2.3Gi RAM

**Explanation**:
- Opening app allocates memory
- Buffered data may be freed automatically
- Available = accurate metric for "can allocate"

---

### Solution 4: Identify High Memory Processes

**Commands**:
```bash
ps aux --sort=-%mem | head -6
```

**Output Example**:
```
USER   PID %CPU %MEM VSZ     RSS    TTY STAT START TIME CMD
root   234  0.2  8.5  1234567 1234M ?   Sl   10:00 2:30 firefox
user   567  0.1  3.2  567890  456M  ?   Ss   10:05 0:45 java
user   890  0.0  1.5  234567  234M  pts Ss   10:10 0:12 bash
```

**Answers**:
- Highest: firefox at 8.5% (1234M RSS)
- %MEM = RSS / Total RAM * 100
- 1234M / 15Gi = ~8.2%

---

### Solution 5: Set Memory Limit

**Commands**:
```bash
# Check limit
ulimit -v

# Set to 100MB
ulimit -v 100000

# This will fail (exceeds limit)
bash -c 'a=($(seq 1 1000000))'

# Remove limit
ulimit -v unlimited
```

**Output Example**:
```
unlimited     # Current (before)
bash: xmalloc: allocator ran out of memory # Error
unlimited     # After removal
```

**Explanation**:
- `ulimit -v` sets virtual memory ceiling
- 100000 KB = ~100 MB
- Allocation fails when exceeds limit
- Useful for preventing runaway processes

---

## Medium Solutions (6-10)

### Solution 6: Understand Page Faults

**Commands**:
```bash
# Show page faults
cat /proc/self/stat | awk '{print "Minor: " $10 ", Major: " $12}'

# Run in loop to see changes
for i in {1..5}; do
  cat /proc/self/stat | awk '{print "Minor: " $10 ", Major: " $12}'
  sleep 1
done
```

**Output Example**:
```
Minor: 4567, Major: 12
Minor: 4589, Major: 12
Minor: 4612, Major: 12
```

**Explanation**:
- Minor faults: page in RAM, just update TLB (cheap)
- Major faults: page on disk, read from disk (expensive)
- Minor increases with normal access (TLB misses)
- Major indicates swapping (bad performance)

---

### Solution 7: Detect Memory Leak

**Script**:
```bash
# Terminal 1: Start process
bash -c 'while true; do a=(${a[@]} $(seq 1 1000)); done'

# Terminal 2: Monitor
watch -n 1 'ps -o pid,vsz,rss,cmd | grep -v grep | tail -2'
```

**Output Example**:
```
PID  VSZ      RSS    CMD
2345 45234    4234   bash -c while
2346 50567    5123   bash -c while
# VSZ and RSS increasing every second
```

**Explanation**:
- VSZ increasing = memory allocated
- RSS increasing = memory in RAM
- Linear growth = memory leak
- Stops growing = memory stabilized

---

### Solution 8: Analyze Memory Map

**Commands**:
```bash
cat /proc/self/maps | head -10
```

**Output Example**:
```
56148d2a2000-56148d2ac000 r-xp /bin/bash
56148d4ab000-56148d4c5000 rw-p /bin/bash
7f3a2e000000-7f3a2e1d1000 rw-p [heap]
7f3a2e400000-7f3a3e400000 rw-p [stack]
```

**Analysis**:
- First: text (r-x = read/execute)
- Second: data (rw- = read/write)
- heap: dynamic allocation (rw-)
- stack: function calls (rw-)

**Explanation**:
- Text: immutable code
- Data: initialized globals
- Heap: malloc/new allocations
- Stack: local variables, function parameters

---

### Solution 9: Calculate Effective Memory

**Commands**:
```bash
# Get values
free -h | grep Mem

# Calculate
free | awk 'NR==2 {
  printf "Used: %d KB\n", $3
  printf "Available: %d KB\n", $7
  printf "Percent: %.1f%%\n", ($3/$2)*100
  printf "Overhead: %d KB\n", ($2-$7)
}'
```

**Output Example**:
```
              total  used   free  available
Mem:          15Gi  4.2Gi 10Gi  10.5Gi

Used: 4300 KB
Available: 10700 KB
Percent: 28.7%
Overhead: 4500 KB
```

**Explanation**:
- Overhead = kernel + buffers/cache
- Available > free = buffers can be freed
- True free = available (not free)

---

### Solution 10: Measure Memory Overhead

**Commands**:
```bash
# Initial state
echo "Processes: $(ps aux | wc -l)"
free -h | grep Mem

# Start 5 bash processes
for i in {1..5}; do bash & done

# Check again
sleep 1
echo "Processes: $(ps aux | wc -l)"
free -h | grep Mem

# Calculate per-process
free | awk 'NR==2 {printf "Per-process: %d KB\n", $3/(NR-1)}'
```

**Output Example**:
```
Processes: 45      # Before (many system processes)
Mem: total 15Gi
Processes: 50      # After (+5 bash processes)
Mem: total 15Gi
Per-process: ~400 KB (shared libs, minimal per-process)
```

**Explanation**:
- Each bash process small due to shared libraries
- Shared memory (libc, etc.) counted once
- VSZ large (includes shared), RSS small (actual unique)

---

## Practice Tips

1. Use `watch` for live monitoring: `watch -n 1 'free -h'`
2. Monitor page faults: `watch -n 1 'cat /proc/[pid]/stat | awk ...'`
3. Track memory over time: `free -h >> log.txt` in loop
4. Use `pmap` for detailed breakdown: `pmap -x [pid]`
5. Compare RSS before/after to see allocation
