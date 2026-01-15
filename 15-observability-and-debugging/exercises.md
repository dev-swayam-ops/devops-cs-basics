# 15 - Observability and Debugging Exercises

## Easy Exercises (1-5)

### Exercise 1: View System Logs
**Objective**: Access system information.

**Task**:
- Run: `journalctl -n 30`
- What's shown? (last 30 messages)
- What's timestamp format?
- What's log level? (DEBUG, INFO, ERROR?)
- Any errors or warnings?

---

### Exercise 2: Follow Live Logs
**Objective**: Monitor in real-time.

**Task**:
- Run: `journalctl -f`
- Generate event: `logger "test message"`
- Does it appear?
- Press Ctrl+C to exit
- Useful for debugging?

---

### Exercise 3: Search Logs by Pattern
**Objective**: Find specific events.

**Task**:
- Run: `journalctl | grep error`
- Or: `grep ERROR /var/log/syslog`
- How many errors?
- When did they occur? (timestamp)
- What caused them?

---

### Exercise 4: Check Log Levels
**Objective**: Understand verbosity.

**Task**:
- DEBUG: detailed diagnostic
- INFO: general information
- WARN: attention needed
- ERROR: problems
- Which level is default? (usually INFO)

---

### Exercise 5: Monitor Running Process
**Objective**: Watch CPU and memory.

**Task**:
- Start: `sleep 100 &`
- Monitor: `top -p [PID]`
- What's %CPU? (should be 0)
- What's %MEM?
- Press q to exit

---

## Medium Exercises (6-10)

### Exercise 6: Trace System Calls
**Objective**: Debug at OS level.

**Task**:
- Run: `strace -e trace=open sleep 1`
- What files opened?
- How many open calls? (could be 100+)
- What's first/last?

---

### Exercise 7: Count System Calls
**Objective**: Profile efficiency.

**Task**:
- Run: `strace -c sleep 1`
- What's most common syscall?
- Which takes most time?
- Optimization insights?

---

### Exercise 8: Trace Library Calls
**Objective**: See function calls.

**Task**:
- Run: `ltrace -e printf echo "hello"`
- What functions called?
- How many library calls?
- Difference from strace?

---

### Exercise 9: Find Memory Leaks
**Objective**: Detect resource waste.

**Task**:
- Run large process: `python -c 'import sys; a=[]; [a.append(list(range(1000))) for _ in range(1000)]; input()'`
- Monitor: `watch -n 1 'ps -o pid,vsz,rss -p [PID]'`
- Is memory growing? (VSZ or RSS?)
- Leak behavior? (growing continuously)

---

### Exercise 10: Analyze Performance Bottleneck
**Objective**: Identify slow operation.

**Task**:
- Slow script: find what's slow
- Use: time, strace, top, flamegraph
- Which syscall slowest?
- Which function slowest?
- Optimization strategy?

---

## Submission Tips

1. journalctl: system logs (JSON, filterable)
2. tail: follow files (less powerful)
3. strace: syscalls (see OS interaction)
4. ltrace: library calls (see code calls)
5. top: real-time monitoring
