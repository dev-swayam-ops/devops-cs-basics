# 03 - Threads and Concurrency

## What You'll Learn

- Thread basics and creation
- Synchronization primitives (mutex, semaphore)
- Race conditions and deadlocks
- Critical sections and atomic operations
- Thread pools and work distribution
- Performance implications of concurrency

## Prerequisites

- Completed **02-process-management**
- Understanding of processes and CPU
- Basic shell scripting knowledge

## Key Concepts

### 1. Threads vs Processes

Threads and processes are both units of execution, but they differ fundamentally in how they share resources.

**Threads**
- Lightweight units of execution within a process
- All threads in a process share the same memory space, file descriptors, and resources
- Each thread has its own stack, program counter, and registers (CPU state)
- Much cheaper to create than a process - no need to duplicate entire memory
- Much faster to switch between threads (context switch is minimal)
- Sharing memory makes communication between threads fast (just read/write shared variables)
- But sharing memory creates danger - simultaneous access can corrupt data
- Useful for I/O-bound tasks within a single application

**Processes**
- Heavyweight units of execution, completely isolated from each other
- Each process has its own complete memory space - 100% separate
- One process cannot directly access another's memory (requires inter-process communication)
- More expensive to create - kernel must allocate complete memory and resources
- Context switching between processes is more expensive than threads
- Communication between processes requires explicit mechanisms (pipes, sockets, shared memory)
- Isolation provides safety and security - crash in one doesn't affect others
- Useful for running independent programs

**Comparison**
- **Creation time**: Threads much faster (microseconds vs milliseconds)
- **Memory overhead**: Threads minimal (just stack), processes significant (full address space)
- **Context switch time**: Threads faster, processes slower
- **Safety**: Processes isolated (safer), threads shared (needs care)
- **Communication**: Threads trivial (shared memory), processes complex (IPC mechanisms)

**When to Use**
- Use **processes** for independent applications, security isolation, or multi-user systems
- Use **threads** for parallel work within same application, like web server handling multiple requests
- Modern web servers (nginx, Node.js) use threads/async I/O instead of processes for efficiency

### 2. Race Conditions

A race condition occurs when multiple threads access shared data simultaneously, and the final result depends unpredictably on the order of execution.

**What Happens**
- Thread A and Thread B both work with the same variable (e.g., a counter)
- Thread A reads value (let's say it's 5)
- Meanwhile, Thread B also reads value (still 5)
- Thread A increments and writes back 6
- Thread B increments and writes back 6
- Expected result: counter = 7 (two increments)
- Actual result: counter = 6 (one increment lost!)
- The "race" is which thread finishes writing first

**Why It's Bad**
- Results are unpredictable and non-deterministic
- Same code may work perfectly most of the time, then fail mysteriously
- Bugs only appear under specific timing/load conditions - very hard to debug
- Can cause data corruption, lost updates, inconsistent state
- Problems compound in complex systems with many threads and data structures

**Real-World Examples**
- Bank: Two ATM withdrawals from same account simultaneously, both see $100 balance, both withdraw $50, account shows $50 instead of $0
- Database: Two processes updating same record, each reads old value, modifies it, writes back - second write overwrites first
- Web server: Multiple threads incrementing request counter, some increments lost due to race

**Prevention**
- Use synchronization primitives (locks, mutexes) to ensure only one thread accesses data at a time
- Make critical sections as small as possible to minimize lock contention
- Atomic operations provide hardware support for indivisible operations
- Immutable data (read-only) never needs locks - no risk of race

### 3. Synchronization Primitives

Synchronization primitives are mechanisms that coordinate access to shared resources among multiple threads.

**Mutex (Mutual Exclusion Lock)**
- Binary lock: either locked or unlocked (two states)
- Only one thread can hold the lock at a time
- Thread must acquire the lock before entering critical section
- If lock is held, thread blocks (waits) until released
- When thread exits critical section, it releases the lock
- Other waiting threads wake up and compete for the lock
- Simple but potentially inefficient if many threads are waiting
- Use when: protecting single shared resource

**Semaphore**
- Counter-based synchronization primitive
- Can be initialized to any value (binary semaphore = 0 or 1, counting semaphore = any number)
- Operations: `wait()` (decrement) and `signal()` (increment)
- `wait()`: If counter > 0, decrement and proceed; if 0, block until another thread signals
- `signal()`: Increment counter; if threads waiting, wake one up
- Example: Semaphore initialized to 3 allows exactly 3 threads to proceed (producer-consumer pattern)
- More flexible than mutex - can limit access to N resources instead of just 1
- Use when: controlling access to pool of identical resources (database connections, worker threads)

**Critical Section**
- The code block where shared data is accessed
- Must be protected by a lock - only one thread can execute at a time
- Should be as small as possible to reduce contention and improve performance
- Example: In a counter increment, just reading, incrementing, and writing back
- Keeping critical sections small means other threads wait less time

**Lock Ordering**
- When multiple locks exist, threads must acquire them in a consistent, defined order
- Prevents circular wait that leads to deadlock
- Example: Always acquire lock A before lock B, never acquire B then wait for A
- All threads must follow same ordering - no exceptions
- Documented and enforced in code

**Atomic Operations**
- Hardware support for indivisible operations that cannot be interrupted mid-operation
- Example: Atomic increment directly in CPU, cannot be partially done
- No lock needed - hardware guarantees atomicity
- Available only for simple operations (increment, compare-and-swap)
- Faster than locks when applicable, but limited in scope
- Use for counters and flags, not complex data structures

### 4. Deadlock Prevention

Deadlock occurs when threads are mutually waiting for resources held by each other, resulting in permanent stall.

**What Is Deadlock**
- Thread A holds lock X and waits for lock Y
- Thread B holds lock Y and waits for lock X
- Both threads are blocked forever - neither can proceed
- System appears frozen; neither thread progresses
- No error is raised - deadlock is silent failure
- Processes hang indefinitely unless manually killed

**Deadlock Conditions (All Must Be Present)**
1. **Mutual Exclusion**: Resource (lock) can only be held by one thread
2. **Hold and Wait**: Thread holding resource waits for another resource
3. **No Preemption**: Resources cannot be forcibly taken from thread
4. **Circular Wait**: Circular chain of threads, each waiting for resource held by next

**Prevention Strategies**

**Lock Ordering (Prevent Circular Wait)**
- Establish global ordering of all locks (e.g., lock A, then B, then C)
- Every thread must acquire locks in this order
- Breaks the circular wait condition
- Example: Threads accessing accounts in order by account ID prevents deadlock
- Simple but requires discipline - developers must follow ordering

**Timeouts (Prevent Indefinite Wait)**
- Thread waits for lock only for specified time, then gives up
- If timeout expires before acquiring lock, thread releases all locks and retries
- Prevents threads from waiting forever
- Disadvantage: Creates temporary slowdown during retry, might still fail under load
- Use for: Non-critical sections, systems requiring responsiveness

**Resource Limits**
- Limit number of threads trying to acquire locks
- Use thread pools instead of unlimited threads
- Prevents resource exhaustion that triggers deadlock
- Example: Web server with fixed pool of workers limits concurrent requests
- Backpressure: reject new requests if system saturated instead of creating more threads

**Detection and Recovery (Runtime Approach)**
- Periodically check for deadlock situation (detecting circular waits)
- When detected, forcibly break circular wait (kill one thread, restart it)
- Allows system to recover from accidental deadlocks
- Expensive computationally - detection takes CPU cycles
- Use for: Complex systems where static prevention is impossible

**Lock-Free Algorithms**
- Avoid locks entirely using atomic operations and careful synchronization
- Threads never wait for locks - just retry operation
- Deadlock impossible without locks
- Very difficult to implement correctly
- Use for: Performance-critical code, low-latency systems

## Hands-on Lab: Thread Synchronization

### Lab Overview
Demonstrate race conditions and synchronization with file locking.

### Lab Commands

```bash
# 1. Create counter file
COUNTER_FILE="/tmp/counter.txt"
echo "0" > $COUNTER_FILE

# 2. Define increment function with lock
increment() {
  exec 200>"$COUNTER_FILE.lock"
  flock 200  # Acquire lock
  count=$(cat $COUNTER_FILE)
  count=$((count + 1))
  echo $count > $COUNTER_FILE
  flock -u 200  # Release lock
  exec 200>&-
}

# 3. Run 5 concurrent increments
for i in {1..5}; do increment & done
wait

# 4. Check result
cat $COUNTER_FILE
# Expected: 5 (all increments applied)

# 5. View thread information
ps -eLf | head -10
# Expected: Shows thread IDs (LWP column)

# 6. Check thread count of process
grep Threads /proc/$$/status
# Expected: Threads: 1 (current shell)

# 7. Demonstrate race without lock (create script)
cat > /tmp/race.sh << 'EOF'
count=0
for i in {1..100}; do
  count=$((count + 1))
done
echo $count
EOF

# 8. Run concurrent without synchronization
bash /tmp/race.sh & bash /tmp/race.sh &
wait
# Expected: Both show 100 (separate memory spaces)

# 9. Show lock file in use
ls -la $COUNTER_FILE.lock 2>/dev/null
# Expected: -rw-r--r-- (lock file exists)

# 10. Cleanup
rm -f $COUNTER_FILE $COUNTER_FILE.lock /tmp/race.sh
```

## Validation

Verify your understanding:

```bash
# Understand race conditions?
echo "✓ Race condition concept understood"

# Can use file locks?
flock -x /tmp/test.lock echo "locked" && echo "✓ Locking works"

# Understand thread states?
ps -eLf | head && echo "✓ Thread viewing works"

# Know deadlock prevention?
echo "✓ Lock ordering concept understood"
```

## Cleanup

```bash
killall bash 2>/dev/null
rm -f /tmp/counter.* /tmp/race.sh /tmp/test.lock
```

## Common Mistakes

1. **Forgetting to lock**: Shared data always needs synchronization
2. **Holding locks too long**: Reduces parallelism
3. **Nested locks**: Can cause deadlock if wrong order
4. **No timeout**: Can wait forever in deadlock
5. **Busy-waiting**: Wastes CPU, use sleep instead

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Deadlock (processes hung) | Kill processes, review lock ordering |
| Race condition (inconsistent results) | Add synchronization locks |
| Low performance | Reduce lock time, use RWLock instead |
| Thread explosion | Use thread pool, limit concurrent |
| Data corruption | Verify all accesses protected by locks |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for reference
4. Move to **04-memory-management** after completion

## Additional Resources

- POSIX threads: `man pthreads`
- File locking: `man flock`
- Concurrency: https://en.wikipedia.org/wiki/Concurrency_(computer_science)

