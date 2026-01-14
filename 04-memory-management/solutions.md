# 04 - Memory Management: Solutions

## Solution 1: Memory Layout

### Command Output

```bash
cat /proc/self/maps

Output:
7ffff7dcd000-7ffff7fb8000 r-xp 00000000 ca:01 1971705  /lib64/libc-2.31.so
7fffea9c6000-7fffea9c7000 rw-p 00000000 00:00 0 [heap]
7fffea9c7000-7fffead47000 rw-p 00000000 00:00 0 [heap]
7ffff7fff000-7ffffffff000 rw-p 00000000 00:00 0 [stack]
ffffffffff600000-ffffffffff601000 --xp 00000000 00:00 0 [vsyscall]
```

### Memory Segments

| Segment | Purpose | Growth | Access |
|---------|---------|--------|--------|
| **Text (Code)** | Executable instructions | Downward | Read-only, no writes |
| **Initialized Data** | Global variables initialized in code | Downward | Readable, writable |
| **Uninitialized Data (BSS)** | Global variables (auto-initialized to 0) | Downward | Readable, writable |
| **Heap** | Dynamic memory (malloc/new) | Upward ↑ | Readable, writable |
| **Stack** | Local variables, parameters, return addresses | Downward ↓ | Readable, writable |
| **Memory-Mapped Regions** | Files, libraries, shared memory | Various | Depends |

### Answers

1. **Main segments**: Text, Data, BSS, Heap, Stack
2. **Purpose**: See table above
3. **Growth**: Heap grows UP (increasing addresses); Stack grows DOWN (decreasing addresses)

---

## Solution 2: Check Memory Usage

### Output Example

```
ps aux | grep python3
user    1234  5.2  15.3  1234560  901000 pts/0  S  10:00  0:05 python3

free -h
                total        used        free
Mem:            7.7Gi       6.2Gi       1.2Gi

top - 10:05:20 up 2 days,  3:45, 2 users, load average: 0.45, 0.52, 0.48
PID USER COMMAND           VSZ    RSS %MEM
1234 user python3       1234560  901000  11.3%
```

### Answers

1. **VSZ (Virtual Size)**: Total virtual address space allocated for process (includes swapped, mmapped, and not yet committed)
2. **RSS (Resident Set)**: Actual physical RAM pages the process occupies in memory
3. **Why VSZ > RSS**: 
   - Process allocated memory via `malloc` but hasn't yet used all of it
   - Libraries are mmap'd but unused sections don't consume RSS
   - Data structures reserve space but don't fully populate it

### Example

```bash
# Allocate 1GB but only touch 100MB
python3 << 'EOF'
x = [0] * (1024 * 1024 * 128)  # 1GB allocated (VSZ)
x[0] = 1                        # Only first element used (small RSS)
import time; time.sleep(100)
EOF

# Result: VSZ = 1GB+, RSS = ~10MB
```

---

## Solution 3: Memory Allocation Patterns

### Expected Output

```bash
Initial RSS:
VmRSS:     500 kB

After allocation:
VmRSS:  100512 kB
```

### Answers

1. **RSS increase**: ~100,000 KB (100 MB) increase
2. **All allocated used?**: Yes, we accessed the array, so all pages are faulted in
3. **Allocated vs used**: 
   - Allocated = requested from OS
   - Used = actually resident in RAM

---

## Solution 4: Memory Limits

### Expected Output

```bash
# After ulimit -v 500000
Segmentation fault (core dumped)
# Reason: Tried to allocate 1GB but limit is 500MB
```

### Answers

1. **What happened**: Program crashed with "Segmentation fault" when trying to allocate more than limit
2. **Why use limits**: 
   - Prevent runaway processes from consuming all memory
   - Protect system stability
   - Fair resource sharing
   - Security (prevent DoS)
3. **For services**:
   ```bash
   # In systemd service file
   [Service]
   MemoryLimit=1G
   
   # Or in shell
   ulimit -v 1000000  # Set 1GB limit
   systemctl start myservice
   ```

---

## Solution 5: Swap Usage

### Output Sequence

```
# Before
free -h
Mem:  7.7Gi  1.2Gi  5.2Gi
Swap: 2.0Gi  0.0Gi  2.0Gi

# During large allocation
free -h
Mem:  7.7Gi  7.6Gi  100.0Mi
Swap: 2.0Gi  1.5Gi  500.0Mi

# vmstat output
vmstat 1 5
procs -----------memory---------- ---swap--
 r  b   swpd   free   buff  cache   si   so
 0  3  1500000  10000  100   500000  100  200  ← Swap in/out activity
```

### Answers

1. **When swap used**: When physical RAM is full, OS moves inactive pages to disk
2. **Performance impact**: Massive degradation (disk is 1000x slower than RAM)
3. **Detection**:
   ```bash
   # Alert if swap > 50%
   free -h | awk '/^Swap/ {
       total = $2; used = $3;
       pct = (used / total) * 100
       if (pct > 50) print "ALERT: High swap usage: " pct "%"
   }'
   ```

---

## Solution 6: Page Faults

### strace Output

```bash
strace -c ls /etc
% time     seconds  usecs/call     calls    errors name
------ ----------- ----------- --------- --------- ----------------
 20.30    0.000203        23.7        21           brk
 15.50    0.000155        18.1        18           mmap2
  ...

# mmap = memory mapping (allocate pages)
# brk = extend heap
```

### Answers

1. **Page fault**: When process accesses unmapped memory, OS must map the page in
2. **Major vs minor**:
   - **Minor**: Page is in RAM but not yet in process's page table
   - **Major**: Page is on disk (swap), must be read from disk
3. **Performance impact**: Major faults are very slow (disk I/O); too many = system is thrashing

---

## Solution 7: Memory Leaks

### Detection Method

```bash
# Monitor RSS over time
watch -n 5 'cat /proc/[PID]/status | grep VmRSS'

# If RSS constantly increases → memory leak
```

### Answers

1. **Memory leak**: Memory is allocated but never freed, gradually consuming all RAM
2. **Detection**: RSS grows without bound over time
3. **Fix**:
   ```python
   # Remove circular references
   obj['ref'] = None  # Break cycle
   
   # Or use garbage collector
   import gc
   gc.collect()  # Force garbage collection
   ```

---

## Solution 8: Cache Efficiency

### Expected Output

```
Sequential: 0.050s
Random: 0.500s
Ratio: 10x slower!
```

### Answers

1. **Why sequential faster**: CPU cache prediction; processor prefetches next sequential lines
2. **Cache locality**: Accessing nearby memory is faster (temporal/spatial locality)
3. **Optimization**:
   - **Spatial**: Access array sequentially, not randomly
   - **Temporal**: Reuse data soon after first access
   - **Example**:
   ```python
   # BAD: Cache misses
   for i in range(1000000):
       process(random_array[random_index])
   
   # GOOD: Sequential (cache hits)
   for i in range(1000000):
       process(sequential_array[i])
   ```

---

## Solution 9: Memory Protection

### Expected Output

```bash
Segmentation fault (core dumped)
# OS prevented access to invalid memory
```

### Output of `cat /proc/$$/maps`

```
562ddea9b000-562ddea9d000 r--p 00000000 ca:01 1234567 /bin/cat
562ddea9d000-562deaa2b000 r-xp 00002000 ca:01 1234567 /bin/cat
562deaa2b000-562deaa34000 r--p 00090000 ca:01 1234567 /bin/cat
562deaa35000-562deaa37000 rw-p 00099000 ca:01 1234567 /bin/cat
7ffd78c0a000-7ffd78c2b000 rw-p 00000000 00:00 0        [stack]
```

### Answers

1. **Memory protection**: OS enforces access controls (read, write, execute)
2. **Why important**: Prevents:
   - One process corrupting another's memory
   - Executing data as code (shellcode attack)
   - Writing to read-only regions
3. **Accessing unallocated memory**: Segmentation fault (SIGSEGV signal)

---

## Solution 10: Memory Profiling

### Output

```
Line #      Mem usage    Increment   Line Contents
     1      50.0 MiB      50.0 MiB   from memory_profiler import profile
     5      50.0 MiB       0.0 MiB   @profile
     6      50.0 MiB       0.0 MiB   def allocate():
     7     127.5 MiB      77.5 MiB       x = [0] * 10000000
     8     205.0 MiB      77.5 MiB       y = [0] * 20000000
```

### Answers

1. **What profiler shows**: Memory used at each line, increment per line
2. **Where most used**: Line 8 allocates 20M array
3. **Optimization**: Use generators instead of lists, use numpy arrays, delete unused objects

---
