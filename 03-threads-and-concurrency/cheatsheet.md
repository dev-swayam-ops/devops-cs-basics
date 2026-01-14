# 03 - Threads and Concurrency: Cheatsheet

## Threading Concepts

| Concept | Definition | Example |
|---------|-----------|---------|
| Thread | Smallest execution unit within a process | Bash background job: `cmd &` |
| Process | Container for threads with own memory | `bash` shell running on system |
| Multithreading | Multiple threads executing concurrently | Web server handling 100 requests |
| Context Switch | OS switches CPU between threads | Happens every few milliseconds |
| Race Condition | Multiple threads accessing shared data | `counter++` without locks |
| Mutex | Mutual exclusion lock | `flock` in bash |
| Semaphore | Counter limiting resource access | Limiting 5 concurrent connections |
| Deadlock | Threads waiting for locks indefinitely | Thread A waits for B, B waits for A |
| Atomic Operation | Indivisible operation (no interruption) | `mkdir` creates dir or fails (atomic) |

---

## Synchronization Primitives

### Mutex (Mutual Exclusion Lock)

```bash
# Acquire lock
flock -x 100

# Critical section (only one thread here)
counter=$((counter + 1))

# Release lock
flock -u 100
```

### File-Based Lock Pattern

```bash
# Get exclusive lock (blocks if locked)
flock -x -w 5 100 || exit 1  # -w 5 = timeout 5 seconds

# Critical section
[code here - protected]

# Release
flock -u 100
```

### Directory-Based Atomic Lock

```bash
# Acquire (mkdir is atomic)
while ! mkdir "$LOCK_DIR" 2>/dev/null; do
    sleep 0.1
done

# Critical section
[code here]

# Release
rmdir "$LOCK_DIR"
```

---

## Thread Lifecycle

```
New → Ready → Running → Waiting → Terminated
      ↑       ↓ ↓ ↑
      └───────┘ └──────
      
States:
- New: Just created
- Ready: Waiting for CPU
- Running: Currently executing
- Waiting: Blocked on I/O or lock
- Terminated: Execution complete
```

---

## Common Concurrency Problems

### 1. Race Condition

```bash
# WRONG (not thread-safe):
value=$(cat $FILE)
echo $((value + 1)) > $FILE

# RIGHT (thread-safe):
flock -x 100
value=$(cat $FILE)
echo $((value + 1)) > $FILE
flock -u 100
```

### 2. Deadlock

```bash
# WRONG (can deadlock):
(
    lock(A); lock(B)
) &
(
    lock(B); lock(A)  # Opposite order!
) &

# RIGHT (same order):
(
    lock(A); lock(B)
) &
(
    lock(A); lock(B)  # Same order
) &
```

### 3. Starvation

```bash
# When low-priority threads never get CPU time
# Solution: Use fair scheduling or priority levels
```

### 4. Priority Inversion

```bash
# High-priority thread blocked waiting for low-priority thread's lock
# Solution: Raise low-priority thread priority while it holds lock
```

---

## Thread Safety Checklist

```
✓ Identify shared resources
✓ Protect with locks/semaphores
✓ Ensure lock ordering to prevent deadlock
✓ Use timeouts on locks (prevent hangs)
✓ Test with multiple threads simultaneously
✓ Monitor for locks held too long
✓ Log access for debugging
```

---

## Linux Thread Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ps -eLf` | Show threads | `ps -eLf \| grep bash` |
| `ps -L` | Show thread IDs | `ps -L -p [PID]` |
| `cat /proc/[PID]/status` | Show thread count | `grep Threads /proc/[PID]/status` |
| `flock` | File-based locking | `flock -x 100` |
| `mutex` | Thread-safe operations | Used in C programs |
| `pthreads` | POSIX threading library | `gcc -pthread ...` |
| `thread_create` | Create thread | `pthread_create()` in C |
| `thread_join` | Wait for thread | `pthread_join()` in C |

---

## Bash Concurrency Patterns

### Background Jobs

```bash
# Start background job
command &

# Get job ID
sleep 100 &
PID=$!

# Wait for all jobs
wait

# Wait for specific job
wait $PID

# Get exit code
echo $?
```

### Pipe Parallelism

```bash
# Producer and consumer
producer | consumer &
wait
```

### Named Pipes (FIFO)

```bash
mkfifo /tmp/pipe
producer > /tmp/pipe &
consumer < /tmp/pipe
wait
```

### Process Substitution

```bash
# Feed output as input
compare <(command1) <(command2)
```

---

## Thread vs Process Trade-offs

| Aspect | Threads | Processes |
|--------|---------|-----------|
| Memory | Shared | Separate |
| Creation | Fast | Slow |
| Context Switch | Fast | Slow |
| Safety | Hard (need locks) | Easy (isolated) |
| Debugging | Difficult | Easier |
| IPC | Easy (shared memory) | Hard (pipes/sockets) |
| Isolation | None | Complete |
| Resource Usage | Low | High |

---

## Synchronization Strategies

### 1. Lock-Based (Pessimistic)

```bash
lock()
shared_resource++
unlock()
```

Assumes conflict; always locks before access.

### 2. Lock-Free (Optimistic)

```bash
old_value = atomic_read()
new_value = old_value + 1
success = atomic_compare_and_swap(old_value, new_value)
if not success: retry
```

Assumes no conflict; retries if conflict detected.

### 3. Read-Write Locks

```bash
# Many readers OR one writer
read_lock()
read_value = shared_resource
read_unlock()

write_lock()
shared_resource++
write_unlock()
```

### 4. Semaphores

```bash
# Limit concurrent access to N
semaphore_wait()  # Decrements counter
[use resource]
semaphore_signal()  # Increments counter
```

---

## Debugging Multi-threaded Code

```bash
# Trace system calls and signals
strace -f -e trace=futex ./program

# Show thread info
cat /proc/[PID]/task/*/comm

# Monitor thread creation
watch -n 1 'cat /proc/[PID]/status | grep Threads'

# Find threads waiting on locks
lsof -p [PID]

# Deadlock detection (no easy way in bash)
# Timeout and alert approach:
timeout 10 lock_operation || echo "DEADLOCK ALERT"
```

---

## Performance Optimization Tips

```bash
# 1. Minimize lock contention
# Lock only critical section, not entire operation
flock -x 100
x=$((x + 1))  # Small critical section
flock -u 100

# 2. Use lock hierarchies
# Always acquire locks in same order

# 3. Reduce lock duration
# Cache values, batch operations

# 4. Use lock-free data structures if possible
# Atomic operations instead of locks

# 5. Monitor lock contention
# Log lock acquisition times
start=$(date +%s%N)
flock -x 100
acquired=$(date +%s%N)
echo "Wait time: $(((acquired - start) / 1000000))ms"
```

---

## Thread Safety in Bash Scripts

```bash
# SAFE: Using flock
{
    flock -x 200
    echo "$data" >> shared_file
} 200>/tmp/shared.lock

# UNSAFE: No locking
echo "$data" >> shared_file

# SAFE: Using atomic mkdir
while ! mkdir /tmp/lock; do sleep 0.1; done
[critical section]
rmdir /tmp/lock

# SAFE: Using atomic file operations
echo "new_value" > /tmp/file.tmp
mv /tmp/file.tmp /tmp/file  # Atomic on same filesystem
```

---

## Useful One-liners

```bash
# Count threads in a process
cat /proc/[PID]/status | grep Threads

# Monitor thread creation in real-time
watch -n 1 'cat /proc/[PID]/status | grep Threads'

# Find processes with most threads
ps -eLf | awk '{print $2}' | sort | uniq -c | sort -rn | head -5

# Stress test with 100 concurrent tasks
for i in {1..100}; do (sleep 1) & done; wait

# Show thread-per-request overhead
ps -p [WEB_SERVER_PID] -L | wc -l
```

---
