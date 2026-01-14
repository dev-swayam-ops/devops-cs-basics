# 03 - Threads and Concurrency: Solutions

## Solution 1: Understand Threading Basics

### Answers

**What is a thread?**
A thread is the smallest unit of execution within a process. It shares the same memory and resources as other threads in the same process but has its own stack and execution state.

**How do threads differ from processes?**
- **Processes**: Heavy-weight, separate memory space, slower creation, inter-process communication required
- **Threads**: Light-weight, shared memory space, faster creation, easy communication through shared memory

**What is multithreading?**
Running multiple threads concurrently within the same process to perform multiple tasks simultaneously.

**3 Advantages**:
1. Lighter weight than processes (less resource consumption)
2. Faster context switching
3. Easier inter-thread communication through shared memory

---

## Solution 2: Identify Threads

### Commands and Output

```bash
ps -eLf | grep bash
# Shows threads (LWP column = Light Weight Process)

ps -eLf | grep systemd
# Shows systemd with multiple threads

cat /proc/[PID]/status | grep Threads
# Output example: Threads:  4
```

### Answers

1. **Bash threads**: Usually 1 (single-threaded)
2. **Systemd threads**: Multiple (5-20 depending on system)
3. **Command to show count**: `cat /proc/[PID]/status | grep Threads`

---

## Solution 3: Race Condition Scenario

### The Problem

```bash
counter=0
for i in {1..100}; do
    ((counter++)) &  # Run in background
done
wait
echo $counter  # Output might be 87 instead of 100
```

### Answers

1. **Why less than 100**: Each `((counter++))` reads the current value, increments it, and writes it back. If multiple threads do this simultaneously without locks, updates are lost.

2. **Race condition**: Multiple threads/processes accessing and modifying the same resource without synchronization, leading to unpredictable results.

3. **How it relates**: Each `&` creates a shell subprocess (not threads, but same concurrency problem).

### Thread-Safe Solution

```bash
counter=0
counter_lock="/tmp/counter.lock"

safe_increment() {
    (
        flock -x 100
        counter=$((counter + 1))
        echo $counter > /tmp/counter_value
    ) 100>$counter_lock
}

for i in {1..100}; do
    safe_increment &
done
wait
echo "Final: $(cat /tmp/counter_value)"  # Always 100
```

---

## Solution 4: Thread Safety

### Answers

**What is thread safety?**
Code is thread-safe if it can be called by multiple threads simultaneously without data corruption or race conditions. Shared resources must be protected with locks.

**Why NOT thread-safe:**
```bash
# Problem:
value=$(cat $FILE)      # Thread A reads: 5
# Context switch
sleep 0.1               # Simulates processing
                        # Thread B reads: 5, writes: 6
# Back to Thread A
echo $((value + 1)) > $FILE  # Writes: 6 (data lost!)
```

**How to fix:**
```bash
# Use atomic operations or locks
flock -x 100
value=$(cat $FILE)
echo $((value + 1)) > $FILE
flock -u 100
```

**Mutex/Lock:**
A mechanism that ensures only one thread can access a resource at a time. `flock` is Linux's file-based lock.

---

## Solution 5: Mutual Exclusion

### Expected Output

```bash
# Without lock:
# Final value: 3 (expected: 10)

# With flock:
# Final value: 10 (correct!)
```

### How flock Works

```bash
exec 200> $LOCK          # Open file descriptor 200
flock 200                # Acquire exclusive lock
# Critical section (only one thread here)
flock -u 200             # Release lock
```

---

## Solution 6: Deadlock

### What Happens

The script will hang indefinitely. Thread 1 holds lock A and waits for B; Thread 2 holds lock B and waits for A. Neither can proceed.

### Answers

**What is a deadlock?**
Two or more threads waiting for locks that the other holds, causing them to wait forever.

**Prevention strategies:**
1. **Lock ordering**: Always acquire locks in same order
2. **Timeouts**: Use `flock -w timeout`
3. **Lock hierarchies**: Establish rules for lock acquisition
4. **Resource pooling**: Avoid circular dependencies

### Fixed Version

```bash
# Both threads acquire locks in same order (A then B)
(
    exec 300>/tmp/resource_a
    exec 301>/tmp/resource_b
    flock 300
    flock 301
    # Safe to use both resources
) &
```

---

## Solution 7: Thread Pool

### Answers

**What is a thread pool?**
A fixed number of pre-created threads that repeatedly take tasks from a queue and execute them. Avoids overhead of creating new threads for each task.

**Workers**: 4 (POOL_SIZE=4)

**Performance benefit**:
- Threads are reused (no creation/destruction overhead)
- Limits concurrent execution
- Better resource utilization

### Improved Version with Better Performance

```bash
# Reduce semaphore delays for better throughput
worker() {
    timeout 30 cat < $WORK_QUEUE | while read task; do
        echo "Processing: $task" >> $RESULTS
        sleep 0.5
    done
}
```

---

## Solution 8: Semaphore Pattern

### Answers

**What is a semaphore?**
A counter that limits access to a resource. When counter > 0, a thread can acquire it (counter--). When 0, threads must wait. Releasing increments counter.

**How it limits access:**
`MAX_PERMITS=2` ensures only 2 tasks access the resource simultaneously.

**Why useful:**
- Control resource pool access
- Prevent resource exhaustion
- Rate limiting

### Better Implementation (Bash doesn't have true semaphores)

```bash
# Use /tmp directory for atomic operations
SEMAPHORE_DIR="/tmp/sems"
mkdir -p $SEMAPHORE_DIR

acquire() {
    while ! mkdir "$SEMAPHORE_DIR/permit_$1" 2>/dev/null; do
        sleep 0.1
    done
}

release() {
    rmdir "$SEMAPHORE_DIR/permit_$1" 2>/dev/null
}
```

---

## Solution 9: Producer-Consumer

### Expected Behavior

```
Produced: Item-1
Consumed: Item-1
Produced: Item-2
Produced: Item-3
Consumed: Item-2
[...]
```

### Issues with Current Implementation

1. Not truly thread-safe (multiple writers/readers to same file)
2. Race conditions possible when checking/modifying buffer
3. No proper synchronization primitives

### Production Implementation (Would use pthread in C)

```c
// Pseudo-code - actual implementation uses condition variables
while (true) {
    lock(buffer);
    while (buffer.size() >= MAX_SIZE) {
        wait(not_full);  // Wait and release lock
    }
    buffer.push(item);
    signal(not_empty);
    unlock(buffer);
}
```

---

## Solution 10: Concurrency Issues in Real Code

### Thread Safety Issues

1. **LOG file**: Multiple threads writing simultaneously → corrupted log
2. **request_count**: Race condition reading/writing counter
3. **No synchronization**: Requests interfere with each other

### Thread-Safe Rewrite

```bash
#!/bin/bash
LOG="/tmp/requests.log"
COUNT_FILE="/tmp/request_count"
LOCK_DIR="/tmp/request_lock"

handle_request() {
    request_id=$1
    
    # Acquire lock
    while ! mkdir "$LOCK_DIR" 2>/dev/null; do
        sleep 0.01
    done
    
    # Critical section (thread-safe)
    echo "Request-$request_id starting" >> $LOG
    count=$(($(cat $COUNT_FILE 2>/dev/null || echo 0) + 1))
    echo $count > $COUNT_FILE
    sleep 1
    echo "Request-$request_id completed" >> $LOG
    
    # Release lock
    rmdir "$LOCK_DIR"
}

echo "0" > $COUNT_FILE
for i in {1..10}; do
    handle_request $i &
done
wait
echo "Total: $(cat $COUNT_FILE)"
```

### Production Approach

```bash
# Use proper logging with date timestamps
log_request() {
    echo "$(date '+%Y-%m-%d %H:%M:%S.%N') - Request-$1" >> $LOG
}

# Use atomic operations
atomic_increment() {
    flock -x -w 5 100
    count=$(($(cat $COUNT_FILE || echo 0) + 1))
    echo $count > $COUNT_FILE
    flock -u 100
} 100>$COUNT_FILE.lock

# Add error handling and monitoring
handle_request_safe() {
    request_id=$1
    start_time=$(date +%s%N)
    
    log_request "START-$request_id"
    
    if ! timeout 30 do_work "$request_id"; then
        log_request "TIMEOUT-$request_id"
        return 1
    fi
    
    atomic_increment
    end_time=$(date +%s%N)
    duration=$((($end_time - $start_time) / 1000000))
    echo "Request-$request_id took ${duration}ms" >> /tmp/metrics.log
    
    log_request "COMPLETE-$request_id"
}
```

---
