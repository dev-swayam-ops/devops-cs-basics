# 03 - Threads and Concurrency Exercises

## Easy Exercises (1-5)

### Exercise 1: Understand Race Condition
**Objective**: Observe unpredictable behavior without synchronization.

**Task**:
- Create a script that increments a variable 1000 times
- Run two instances concurrently in background
- Compare results - are they the same each time?
- What should the result be?
- Why does it vary?

---

### Exercise 2: Use File Locking
**Objective**: Apply synchronization to prevent race conditions.

**Task**:
- Create a counter file (e.g., `/tmp/count.txt` = 0)
- Write a function that: locks, increments, unlocks
- Run 5 instances concurrently
- Check final result (should be 5)
- Verify lock file was created/used

---

### Exercise 3: Compare Thread vs Process
**Objective**: Understand differences between threads and processes.

**Task**:
- Show bash process info: `ps -eLf | grep bash | head -3`
- Show thread count: `grep Threads /proc/$$/status`
- Start a background process: `bash &`
- How many threads does it have?
- What's the difference in memory?

---

### Exercise 4: Observe Lock Blocking
**Objective**: See how locks prevent concurrent access.

**Task**:
- Create a critical section with flock
- Have one process hold lock for 5 seconds
- Try to acquire lock from another terminal
- Time how long second process waits
- After first releases, second acquires immediately

---

### Exercise 5: Demonstrate Critical Section
**Objective**: Show importance of atomic operations.

**Task**:
- Create shared file with counter = 0
- Write function: read, increment, write
- Run 10 concurrent increments WITHOUT lock
- Run 10 concurrent increments WITH lock
- Compare results in both cases

---

## Medium Exercises (6-10)

### Exercise 6: Deadlock Scenario
**Objective**: Create and understand deadlock.

**Task**:
- Create two lock files: lock1, lock2
- Process A: acquire lock1, wait 2s, acquire lock2
- Process B: acquire lock2, wait 2s, acquire lock1
- Run both concurrently
- Both processes hang (deadlock!)
- Kill with Ctrl+C or from another terminal

---

### Exercise 7: Lock Ordering Prevention
**Objective**: Fix deadlock with consistent lock ordering.

**Task**:
- Modify Exercise 6: always acquire locks in same order
- Process A: lock1 then lock2
- Process B: lock1 then lock2 (same order!)
- Run concurrently
- Processes complete without hanging
- What's the difference?

---

### Exercise 8: Semaphore Simulation
**Objective**: Simulate semaphore with counter.

**Task**:
- Create resource file: `/tmp/semaphore` with count = 3
- Write acquire function: decrement while waiting
- Write release function: increment
- Run 5 concurrent tasks, each holds resource for 1s
- Only 3 should run simultaneously
- Others wait their turn

---

### Exercise 9: Reader-Writer Problem
**Objective**: Understand read vs write locking.

**Task**:
- Data file that's frequently read, rarely written
- Multiple readers access simultaneously (fast)
- Single writer needs exclusive access (rare)
- Readers should block writer, not each other
- Design with flock for write-exclusive scenario

---

### Exercise 10: Measure Lock Contention
**Objective**: Analyze performance impact of locks.

**Task**:
- Write loop that 1000x increments counter
- Measure time WITHOUT lock
- Measure time WITH lock
- Calculate overhead percentage
- Increase threads to 10x, compare overhead
- What conclusion about lock cost?

---

## Submission Tips

1. Test race conditions multiple times (behavior varies)
2. Use `ps -eLf` to track processes and threads
3. Use `lsof /tmp/` to see lock files in use
4. Add timestamps to understand execution order
5. Practice lock ordering to prevent deadlocks
