# 04 - Memory Management: Cheatsheet

## Memory Layout

```
High Address (0xFFFFFFFF)
┌─────────────────────────┐
│   Command Line Args     │
│   Environment Variables │
├─────────────────────────┤
│      STACK ↓            │  Grows downward
│  (Local variables,      │
│   return addresses)     │
│                         │
├─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─┤  Free space (can grow)
│                         │
│      HEAP ↑             │  Grows upward
│  (Dynamic memory)       │
├─────────────────────────┤
│   BSS Segment           │
│   (Uninitialized data)  │
├─────────────────────────┤
│   Initialized Data      │
│   (Global variables)    │
├─────────────────────────┤
│   Text Segment          │
│   (Code, read-only)     │
├─────────────────────────┤
│   Reserved              │
└─────────────────────────┘
Low Address (0x00000000)
```

---

## Memory Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `cat /proc/[PID]/maps` | Show memory regions | Show all mapped memory sections |
| `cat /proc/[PID]/status` | Show memory info | `grep VmRSS` for actual RAM usage |
| `ps aux` | List processes with memory | `--sort=-%mem` for memory usage |
| `free -h` | Show RAM and swap usage | `free -h \| grep Mem` |
| `top` | Monitor memory in real-time | Press 'M' to sort by memory |
| `htop` | Enhanced memory monitoring | More user-friendly than top |
| `pmap [PID]` | Show process memory map | `pmap -x [PID]` for details |
| `ulimit -a` | Show resource limits | `ulimit -v 1000000` to set limit |
| `ulimit -v` | Virtual memory limit | Prevents process from using too much |
| `getconf PAGESIZE` | Show page size | Usually 4KB |
| `/proc/sys/vm/` | Virtual memory settings | Tuning kernel memory behavior |
| `swapoff [device]` | Disable swap | Remove swap from system |
| `swapon [device]` | Enable swap | Add swap space |

---

## Memory Metrics

### VSZ (Virtual Size)

```
Total virtual memory allocated:
VSZ = Text + Data + BSS + Heap + Shared Memory + Mmap'd regions

Example: VSZ = 1GB
- Program allocated 1GB
- But might not be using all of it yet
- Process can address 1GB of memory
```

### RSS (Resident Set Size)

```
Actual RAM pages in memory:
RSS = Pages currently in physical RAM

Example: RSS = 500MB
- 500MB of the allocated memory is actually in RAM
- Rest is on disk (swap) or not yet accessed
- Better metric for actual memory usage
```

### PSS (Proportional Set Size)

```
Fair accounting of shared memory:
PSS = RSS - (Shared Pages / Number of sharers)

More accurate for systems with shared libraries
```

### USS (Unique Set Size)

```
Memory unique to this process:
USS = Memory not shared with any other process

Lowest memory footprint
```

---

## Page and Memory Concepts

### Page

```
Basic unit of memory management
- Typical size: 4KB (4096 bytes)
- Set by CPU architecture
- OS manages memory in pages
```

### Page Fault

```
When process accesses unmapped memory:

1. Process accesses address
2. MMU (Memory Management Unit) checks page table
3. Page not present → Page Fault exception
4. OS handles exception:
   - If on disk (swap): Load from disk
   - If not allocated: Allocate new page
5. Resume process execution

Minor Fault: Page in RAM, just needs mapping
Major Fault: Page on disk, must read (slow!)
```

### Translation Lookaside Buffer (TLB)

```
Cache of virtual-to-physical address mappings
- Very fast (registers)
- Limited size (~1024 entries)
- TLB misses require main memory lookup
```

---

## Memory Allocation Patterns

### Stack Allocation

```bash
function f() {
    int x = 10;  ← Allocated on stack
    return x;
}  ← Automatically freed when function returns
```

**Pros**: Fast, automatic cleanup  
**Cons**: Limited size, not suitable for large objects

### Heap Allocation

```bash
x = malloc(1000);  ← Allocated on heap
...
free(x);          ← Manually freed (important!)
```

**Pros**: Flexible size, lifetime controlled  
**Cons**: Manual management, prone to leaks

### Mmap (Memory-Mapped Files)

```bash
fd = open("file.txt")
ptr = mmap(fd)  ← Map file into memory
# Access like array: ptr[10]
```

**Pros**: Efficient file access  
**Cons**: Must manage page alignment

---

## Memory Optimization Techniques

### 1. Spatial Locality

```python
# BAD: Random access (cache misses)
for i in random_indices:
    process(array[i])

# GOOD: Sequential access (cache hits)
for i in range(len(array)):
    process(array[i])
```

### 2. Temporal Locality

```python
# BAD: Reuse after long interval
data = load_large_data()
... [much code] ...
process(data)  # Data evicted from cache

# GOOD: Use immediately
data = load_large_data()
process(data)  # Still in cache
```

### 3. Working Set Optimization

```bash
# Keep frequently used data smaller than L3 cache
# L1 cache: ~32KB, L2: ~256KB, L3: ~8MB
# Optimize for L3 cache size
```

### 4. Memory Pooling

```bash
# Instead of malloc/free for each object:
pool = create_pool(100)  # Pre-allocate 100 objects
for i in range(1000):
    obj = pool.get()     # Reuse from pool
    use(obj)
    pool.put(obj)        # Return to pool
```

---

## Memory Debugging

### Check for Leaks

```bash
# Monitor RSS over time
watch -n 1 'cat /proc/[PID]/status | grep VmRSS'

# If RSS constantly increases → likely leak
```

### Use valgrind (if available)

```bash
valgrind --leak-check=full ./program
# Shows all memory leaks, lines where allocated
```

### strace for mmap/brk calls

```bash
strace -e trace=mmap,brk ./program
# Shows memory allocation calls
```

---

## Memory Limits and Control

### ulimit Settings

```bash
ulimit -a              # Show all limits
ulimit -v 1000000      # Virtual memory: 1GB
ulimit -m 500000       # Physical memory: 500MB
ulimit -s 8192         # Stack size: 8MB
ulimit -d 500000       # Heap size: 500MB
```

### Cgroups (Container Memory Limits)

```bash
# Create cgroup with 2GB limit
cgcreate -g memory:/myapp
cgset -r memory.limit_in_bytes=2147483648 /myapp

# Run process in cgroup
cgexec -g memory:/myapp ./program
```

### Systemd Service Limits

```ini
[Service]
MemoryLimit=2G
MemoryMax=3G
MemoryHigh=2.5G
MemorySwapMax=1G
```

---

## Swap Behavior and Tuning

### Swappiness (0-100)

```bash
# View current swappiness
cat /proc/sys/vm/swappiness

# Change (0 = prefer RAM, 100 = prefer swap)
echo 10 > /proc/sys/vm/swappiness  # Conservative
echo 50 > /proc/sys/vm/swappiness  # Moderate
echo 90 > /proc/sys/vm/swappiness  # Aggressive
```

### When Swap is Used

```
1. Physical RAM full
2. Pages inactive for long time
3. Swappiness setting
4. Memory pressure

High swap usage → Performance degradation
```

---

## Page Caching

### Buffer vs Cache

```
Buffer Cache:
- Caches disk blocks being read/written
- Facilitates I/O operations

Page Cache:
- Caches file contents
- Used for regular file access
```

### Clear Caches (Dangerous!)

```bash
# Sync data to disk first
sync

# Clear caches (performance hit!)
echo 3 > /proc/sys/vm/drop_caches
# 1 = PageCache, 2 = dentries/inodes, 3 = both
```

---

## Monitoring and Tuning

### Key Metrics

```bash
# Memory pressure
free -h | grep Mem

# Swap usage
free -h | grep Swap

# Page faults
cat /proc/vmstat | grep fault

# OOM (Out of Memory) events
dmesg | grep OOM

# Process page faults
cat /proc/[PID]/stat | awk '{print $9, $10}'
# 9th field: minor faults, 10th: major faults
```

### Performance Indicators

```
Good:
- Low swap usage (<10%)
- Most memory cached (MemAvailable ~= MemFree + Cached)
- Few page faults

Bad:
- High swap usage (>50%)
- Constant major page faults
- Memory pressure increasing
```

---

## Useful One-liners

```bash
# Find processes using most RAM
ps aux --sort=-%mem | head -5

# Monitor swap in real-time
watch -n 1 'free -h | grep Swap'

# Detailed memory info
cat /proc/[PID]/status | grep Vm

# Get page fault rate
watch -n 1 'cat /proc/vmstat | grep fault'

# Find memory-leaking process
for pid in $(pgrep -f pattern); do
  rss=$(cat /proc/$pid/status | grep VmRSS | awk '{print $2}')
  echo "PID $pid: ${rss}KB"
done
```

---
