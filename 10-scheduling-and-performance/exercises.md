# 10 - Scheduling and Performance Exercises

## Easy Exercises (1-5)

### Exercise 1: Check Current Load
**Objective**: Understand system load.

**Task**:
- Run: `uptime`
- What are 3 numbers? (1, 5, 15 minute)
- Is load high or low?
- CPU count: `nproc`
- Ratio: load / CPU = ?

---

### Exercise 2: Monitor Load in Real-Time
**Objective**: Watch load changes.

**Task**:
- Run: `watch -n 1 uptime`
- Start CPU work in another terminal
- Does load change?
- How fast does it update?
- Press q to exit

---

### Exercise 3: Check Process Priorities
**Objective**: See nice values.

**Task**:
- Run: `ps -o pid,nice,cmd | head`
- What's nice value of init?
- What's default nice?
- How does range work? (-20 to 19)
- Lower nice = higher or lower priority?

---

### Exercise 4: Start Process with Nice
**Objective**: Create low-priority process.

**Task**:
- Run: `nice -n 10 sleep 100 &`
- Check: `ps -o pid,nice,cmd | grep sleep`
- Is nice 10?
- What about nice -5? (higher priority)
- Can you use negative without root?

---

### Exercise 5: Change Priority with Renice
**Objective**: Adjust running process.

**Task**:
- Start: `sleep 100 &` (default nice 0)
- Get PID: `echo $!`
- Renice: `renice -n 10 -p [PID]`
- Check: `ps -o nice -p [PID]`
- Changed to 10?

---

## Medium Exercises (6-10)

### Exercise 6: Compare Process Scheduling
**Objective**: See priority effects.

**Task**:
- Start high-priority: `nice -n -10 sleep 100 &`
- Start low-priority: `nice -n 10 sleep 100 &`
- CPU usage same?
- Which gets more CPU?
- Scheduler favors high nice?

---

### Exercise 7: Monitor Context Switches
**Objective**: Understand process overhead.

**Task**:
- Run: `cat /proc/self/status | grep ctxt`
- What's voluntary vs non-voluntary?
- Voluntary: I/O blocking
- Non-voluntary: scheduler preemption
- More bad? (many context switches)

---

### Exercise 8: Pin Process to CPU
**Objective**: Control CPU affinity.

**Task**:
- Run: `taskset -c 0 sleep 100 &`
- Pin to CPU 0
- Check: `taskset -cp [PID]`
- Can you pin to multiple CPUs? (-c 0,2)
- Performance impact?

---

### Exercise 9: Load Balance Check
**Objective**: See per-CPU load.

**Task**:
- Run: `top` (press 1 for per-CPU)
- Which CPU has most load?
- Balanced across CPUs?
- Top 5 processes?
- Which process using most CPU?

---

### Exercise 10: Simulate High Load
**Objective**: Create system load.

**Task**:
```bash
stress-ng --cpu 2 --timeout 30s &
```
- Or: `for i in {1..4}; do while true; do :; done & done`
- Monitor: `uptime`
- Load increases?
- Check: `ps aux`
- Kill: `pkill -9 bash` or stress-ng

---

## Submission Tips

1. Use `uptime` for quick load check
2. Use `ps -o nice,cmd` to see priorities
3. Lower nice = higher priority (counterintuitive)
4. Load = CPUs fully used (relative to core count)
5. Context switches overhead, but necessary for I/O
