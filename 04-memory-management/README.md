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

Every process has a memory layout that organizes different types of data in a linear address space. Understanding this structure is essential for memory management.

**Text Segment (Code)**
- The lowest addresses in process memory
- Contains the executable instructions of the program (read-only)
- Cannot be modified at runtime - attempting to write causes segmentation fault
- Same for all instances of the same program
- Size fixed at compile time - cannot grow or shrink

**Data Segment (Initialized Globals)**
- Global and static variables that are explicitly initialized
- Contains known values loaded from executable file
- Readable and writable
- Size fixed at compile time
- Example: `int global_var = 42;` goes here, not in BSS

**BSS Segment (Uninitialized Globals)**
- Global and static variables without explicit initialization (implicitly zero)
- "Block Started by Symbol" - originally an assembly convention
- Not stored in executable file (would waste space storing zeros)
- Kernel initializes to zero when process starts
- Readable and writable
- Much more compact than Data segment - zeros don't take file space
- Size fixed at compile time
- Example: `int global_array[1000000];` goes here

**Heap (Dynamic Allocation)**
- Region for dynamic memory allocation during runtime
- Grows upward (toward higher addresses) when more memory is allocated
- Allocation functions: `malloc()`, `calloc()`, `realloc()` return heap pointers
- Must be explicitly deallocated with `free()` - forgetting causes memory leak
- Multiple allocations can exist - heap is fragmented collection of blocks
- Size grows dynamically as program allocates more memory
- Access is slower than stack - requires pointer dereferencing

**Stack (Function Calls & Local Variables)**
- Region for function calls and local variables
- Grows downward (toward lower addresses) as functions are called
- When function is called, "stack frame" created with return address and local variables
- When function returns, frame is deallocated - automatic cleanup
- Each thread gets its own stack
- Much faster than heap - just moves pointer up/down
- Fixed size per process - overflow causes stack overflow crash
- Data automatically cleaned up when scope ends - no memory leaks possible

**Memory Layout Visualization**
```
Highest Address: [Stack] ↓ (grows downward)
                 (unused space/gap)
                 [Heap] ↑ (grows upward)
                 [BSS] (uninitialized globals, zeros)
                 [Data] (initialized globals)
Lowest Address:  [Text] (code, read-only)
```

### 2. Virtual vs Physical Memory

Modern operating systems use virtual memory to give each process the illusion of having a large, contiguous memory space, independent of actual physical RAM.

**Virtual Memory (Address Space)**
- Each process sees its own private address space starting from address 0
- Addresses in programs are virtual - not directly pointing to RAM
- Process can address up to limit of address space (32-bit: 4GB, 64-bit: 16EB)
- Provides isolation: process A cannot see or modify process B's memory
- Provides protection: OS can make certain pages read-only even in same process
- Two processes can have same virtual address 0x1000 but they point to different physical RAM
- Virtual memory system is transparently managed by kernel

**Physical Memory (RAM)**
- The actual RAM installed in the computer
- Limited resource - typically 8GB, 16GB, 32GB, etc.
- All processes share the same physical memory
- Slower to manufacture and buy than virtual memory can be
- Cannot directly address physical memory from user programs - only through virtual memory

**Address Translation (Virtual to Physical)**
- CPU hardware (Memory Management Unit) translates virtual to physical addresses
- Page table in kernel stores the mapping for each process
- For each memory access, MMU looks up virtual address in page table and gets physical address
- If page table entry missing → page fault (kernel must handle)
- TLB caches frequently used translations for speed

**Paging**
- Virtual memory system divides memory into fixed-size pages (typically 4KB)
- Pages are units of both virtual and physical memory
- If physical RAM is full, kernel selects a page (often least-recently-used) and writes it to disk (swap)
- When that page is accessed again, kernel reads it back from disk into RAM
- Allows systems to run larger applications than physical RAM size
- But disk access is 1000x slower than RAM - paging hurts performance

**TLB (Translation Lookaside Buffer)**
- Hardware cache that stores recent virtual-to-physical address translations
- Speeds up address translation by avoiding page table lookups
- Small but very fast (few CPU cycles)
- On context switch, TLB is flushed (entries are context-specific)
- Cache misses (TLB miss) cause page table lookup (slower)
- Reason why context switching has overhead - TLB must be rebuilt

**Why Virtual Memory Matters**
- Isolation: Prevents one process from corrupting another's memory
- Protection: Can mark pages as read-only, execute-only, etc.
- Flexibility: Processes can be larger than physical RAM
- Relocation: Physical location of process can move without affecting process
- Sharing: OS can map same physical page into multiple processes (copy-on-write)

### 3. Memory Metrics

When analyzing memory usage, several metrics tell different stories. Understanding which to use is critical.

**VSZ (Virtual Set Size)**
- Total virtual memory allocated to the process
- Includes everything: stack, heap, code, libraries, even pages on disk
- Can be much larger than physical RAM - that's normal
- Not useful for understanding actual memory usage - don't be alarmed by large VSZ
- Example: Process with VSZ=2GB might only use 50MB physical RAM

**RSS (Resident Set Size)**
- Physical memory (RAM) currently occupied by this process
- The actual amount of RAM the process is consuming
- Includes all pages currently in physical RAM (stack, heap, code, shared libraries)
- Does not include pages swapped to disk
- Decreases when pages are paged out to disk
- More useful than VSZ for understanding memory pressure
- Caveat: Counts full page even if shared with other processes

**PSS (Proportional Set Size)**
- Fair share of shared memory pages
- Like RSS but shared memory is divided among processes that share it
- Example: If 10 processes share a 1MB library, each counts 100KB in PSS
- Most accurate metric for total system memory usage
- More expensive to calculate (kernel must track sharing)
- Best metric for understanding true memory cost of a process

**Swap**
- Virtual memory pages written to disk because physical RAM was full
- Accessed when OS needs the data - must read from disk (slow)
- Goal: minimize swap usage
- High swap usage indicates system is memory-constrained
- Modern systems try to avoid swap in favor of dropping cache

**Memory Pressure Metrics**
- **Available**: Memory available for new allocations (free + reclaimable cache)
- **Buffers**: Kernel page cache for file I/O
- **Cached**: Filesystem cache of file contents
- **Dirty**: Pages modified but not yet written to disk
- High dirty pages indicate heavy I/O load

### 4. Page Faults

A page fault occurs when a process tries to access a memory page that's not currently in physical RAM. Understanding page faults is essential for performance analysis.

**What Is a Page Fault**
- Process accesses a virtual address
- Kernel checks: is this page in physical RAM?
- If no, kernel must handle the fault - an expensive interrupt
- Kernel either loads page from disk (major fault) or just sets up mapping (minor fault)
- After kernel handles fault, process retries the memory access

**Minor Page Fault (Soft Fault)**
- Page is in physical RAM but needs mapping to be set up
- Very common - happens on first access to any page
- At process startup: all pages unmapped, first access to each causes minor fault
- At fork() time: child initially shares parent's pages via copy-on-write, first write causes minor fault
- Quick to handle (just update page table) - microseconds
- Many minor faults are normal and harmless
- Use `ps` or `/proc/[pid]/stat` field 10 to check

**Major Page Fault (Hard Fault)**
- Page is on disk (swapped out) and must be read from disk into RAM
- Hundred times slower than RAM access - significant performance hit
- Indicates memory pressure - system ran out of physical RAM
- If process consistently getting major faults, consider: more RAM, less processes, optimize memory
- Kernel can pre-fault pages into RAM to prevent future major faults
- Use `ps` or `/proc/[pid]/stat` field 12 to check

**How to Check Page Faults**
```bash
# View detailed fault statistics for a process
cat /proc/[pid]/stat

# Field 10: minor faults
# Field 12: major faults

# Check during program execution
ps -o pid,min_flt,maj_flt,cmd
```

**Performance Impact**
- A few hundred minor faults: negligible impact
- A few major faults: slight delay reading from disk
- Thousands of major faults per second: severe performance degradation
- System swapping heavily: becomes unresponsive

**Optimization Strategies**
- Use enough memory so processes fit in RAM
- Minimize working set size (code and data actually needed)
- Use memory pre-faulting for predictable workloads
- Monitor page faults to detect memory pressure

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

