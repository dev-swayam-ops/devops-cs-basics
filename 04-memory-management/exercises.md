# 04 - Memory Management Exercises

## Easy Exercises (1-5)

### Exercise 1: Check System Memory Status
**Objective**: Understand current memory usage.

**Task**:
- Run `free -h` to see total, used, free memory
- What percentage of RAM is in use?
- How much swap is available?
- Is swap being used?
- What's difference between "free" and "available"?

---

### Exercise 2: View Process Memory Usage
**Objective**: Monitor individual process memory.

**Task**:
- Get your shell PID: `echo $$`
- Show memory: `ps -o pid,vsz,rss,cmd -p [PID]`
- What is VSZ? (should be ~4-5MB for bash)
- What is RSS? (typically 1-2MB)
- Ratio: RSS/VSZ = ?

---

### Exercise 3: Compare Memory Before and After
**Objective**: See how memory changes with activity.

**Task**:
- Check free memory: `free -h`
- Open a large application or file
- Check free memory again
- How much did it change?
- Is change in "used" or "available"?

---

### Exercise 4: Identify High Memory Processes
**Objective**: Find which processes use most memory.

**Task**:
- List top 5 by memory: `ps aux --sort=-%mem | head -6`
- What process uses most?
- How much memory (RSS)?
- What's %MEM value?
- Calculate: RSS / Total RAM = %MEM?

---

### Exercise 5: Set Memory Limit
**Objective**: Control process memory allocation.

**Task**:
- Current limit: `ulimit -v`
- Set limit: `ulimit -v 100000` (100MB)
- Try to allocate: `bash -c 'a=($(seq 1 1000000))'`
- What happens? (should fail)
- Increase limit and try again
- Remove limit: `ulimit -v unlimited`

---

## Medium Exercises (6-10)

### Exercise 6: Understand Page Faults
**Objective**: Observe memory access patterns.

**Task**:
- Get page fault stats: `cat /proc/self/stat | awk '{print "Minor: " $10 ", Major: " $12}'`
- Run memory-intensive task in another terminal
- Check stats again (should increase)
- What's difference: minor vs major fault?
- Which one is slower?

---

### Exercise 7: Detect Memory Leak
**Objective**: Identify growing memory usage.

**Task**:
- Start process: `bash`
- Monitor VSZ: `watch -n 1 'ps -o pid,vsz,cmd | grep bash'` (another terminal)
- Continuously allocate in first terminal
- Is VSZ growing?
- Is RSS growing?
- How quickly?

---

### Exercise 8: Analyze Memory Map
**Objective**: Understand memory regions.

**Task**:
- View memory map: `cat /proc/self/maps | head -10`
- Identify regions: text (code), data, heap, stack
- Each has: address, permissions (r/w/x), offset, inode
- What's difference in permissions?
- Which region is writable?

---

### Exercise 9: Calculate Effective Memory
**Objective**: Understand usable vs theoretical memory.

**Task**:
- Total RAM from `free -h`
- Available from `free -h`
- Difference = kernel reserved + buffers/cache
- What percentage is actually available?
- Start an app, check again
- What gets freed when app needs memory?

---

### Exercise 10: Measure Memory Overhead
**Objective**: See process creation cost.

**Task**:
- Count processes: `ps aux | wc -l`
- Check total memory: `free -h`
- Calculate: total memory / process count
- Start 5 new bash shells
- Recount (more processes, same memory?)
- Shared vs private memory analysis

---

## Submission Tips

1. Use `watch` command to monitor changes in real-time
2. Take screenshots before/after to track changes
3. Compare RSS for same process at different times
4. Use `smem` or `pmap` for detailed memory breakdown
5. Remember: VSZ > RSS always (VSZ includes swap potential)
