# 03 - Threads and Concurrency: Quiz

## Multiple Choice Questions (7 Questions)

### Question 1
What is the main difference between threads and processes?

A) Threads are heavier than processes  
B) Threads share memory with other threads in same process; processes have separate memory  
C) Threads can only run on one CPU  
D) Processes run faster than threads

<details>
<summary>Answer</summary>
<strong>B) Threads share memory with other threads in same process; processes have separate memory</strong>

Threads are lightweight execution units within a process that share the same memory space. Processes are heavier with separate memory, making inter-thread communication easier but requiring synchronization to prevent race conditions.
</details>

---

### Question 2
What is a race condition?

A) When two threads compete for CPU time  
B) Multiple threads accessing and modifying shared data without synchronization  
C) When threads run at different speeds  
D) A condition where threads stop executing

<details>
<summary>Answer</summary>
<strong>B) Multiple threads accessing and modifying shared data without synchronization</strong>

A race condition occurs when the final result depends on the timing of thread execution. For example, if two threads increment a counter without locking, updates can be lost.
</details>

---

### Question 3
What does a mutex (mutual exclusion lock) do?

A) Speeds up thread execution  
B) Ensures only one thread can access a resource at a time  
C) Eliminates the need for threads  
D) Prevents threads from being created

<details>
<summary>Answer</summary>
<strong>B) Ensures only one thread can access a resource at a time</strong>

A mutex is a lock that only one thread can hold at a time. When a thread acquires a mutex, others must wait until it's released, preventing concurrent access to shared resources.
</details>

---

### Question 4
What is a deadlock?

A) A thread that crashes  
B) Two or more threads waiting for locks that the other holds  
C) A thread running too slowly  
D) When all threads finish executing

<details>
<summary>Answer</summary>
<strong>B) Two or more threads waiting for locks that the other holds</strong>

Deadlock occurs when Thread A holds Lock 1 and waits for Lock 2, while Thread B holds Lock 2 and waits for Lock 1. Both wait forever, unable to proceed.
</details>

---

### Question 5
How many threads can hold a single mutex at the same time?

A) As many as needed  
B) 2 (one waiting, one executing)  
C) 1  
D) Depends on CPU count

<details>
<summary>Answer</summary>
<strong>C) 1</strong>

By definition, a mutex is exclusive. Only one thread can hold the lock at any time. Other threads must wait until it's released.
</details>

---

### Question 6
What is the key advantage of using a thread pool?

A) Threads run faster  
B) No context switching needed  
C) Reuses threads instead of creating new ones for each task  
D) Prevents deadlocks

<details>
<summary>Answer</summary>
<strong>C) Reuses threads instead of creating new ones for each task</strong>

A thread pool pre-creates a fixed number of threads that repeatedly take tasks from a queue. This avoids the overhead of creating/destroying threads for each task, improving performance.
</details>

---

### Question 7
In Bash, what is the purpose of `flock`?

A) Delete files  
B) Change file permissions  
C) Implement file-based locking for synchronization  
D) Create directories

<details>
<summary>Answer</summary>
<strong>C) Implement file-based locking for synchronization</strong>

`flock` provides file-based locking to implement mutexes in Bash scripts, ensuring only one process/thread can execute critical sections simultaneously.
</details>

---

## Short Answer Questions (3 Questions)

### Question 8
Explain what happens in this code and why it's not thread-safe. How would you fix it?

```bash
counter=0
for i in {1..100}; do
    ((counter++)) &
done
wait
echo $counter  # Might print 87 instead of 100
```

<details>
<summary>Answer</summary>

**What happens:**
Each `((counter++))` runs as a background process. The operations:
1. Read current value of `counter`
2. Increment it
3. Write back

When multiple processes do this simultaneously without synchronization, some increments are lost.

**Why not thread-safe:**
The increment operation isn't atomic - it can be interrupted between read and write, causing another process to read the old value.

**Fixed version:**
```bash
counter=0
LOCK="/tmp/counter.lock"

for i in {1..100}; do
    (
        flock -x 200
        counter=$((counter + 1))
        echo $counter > /tmp/counter_value
        flock -u 200
    ) 200>$LOCK &
done
wait
cat /tmp/counter_value  # Always 100
```

**Explanation:**
The mutex (`flock`) ensures only one process increments at a time, preventing lost updates.
</details>

---

### Question 9
You're seeing these symptoms in a multi-threaded application:
- Threads are not making progress
- Each thread is waiting for a resource
- No CPU activity
- Has been hung for hours

What is most likely wrong, and how would you diagnose and fix it?

<details>
<summary>Answer</summary>

**Diagnosis: Deadlock**

Symptoms indicate a circular wait:
- Thread A holds Lock 1, waits for Lock 2
- Thread B holds Lock 2, waits for Lock 1

**How to diagnose:**
```bash
# Check if threads are truly stuck
ps -L -p [PID]  # Show all threads

# Check what locks they're waiting for
cat /proc/[PID]/task/*/status | grep -i wait

# Monitor with strace
strace -p [PID]  # See if any system calls are happening

# Set timeout to detect hang
timeout 30 ./app || echo "App hung!"
```

**How to fix:**
1. **Immediate**: Kill the application and restart
2. **Code change**: Enforce lock ordering
```bash
# WRONG (can deadlock):
Thread A: lock(A); lock(B)
Thread B: lock(B); lock(A)  # Different order!

# RIGHT (prevent deadlock):
Thread A: lock(A); lock(B)
Thread B: lock(A); lock(B)  # Same order!
```

3. **Monitoring**: Add timeout on locks
```bash
flock -x -w 5 100 || {
    echo "DEADLOCK DETECTED: Lock acquisition timeout"
    exit 1
}
```

4. **Prevention**: Use lock hierarchies and code reviews
</details>

---

### Question 10
Design a thread-safe logging system that multiple web server threads can write to simultaneously without corrupting the log file.

<details>
<summary>Answer</summary>

**Requirements:**
- Multiple threads writing simultaneously
- No log corruption
- No lost log entries
- Minimal performance impact

**Solution:**

```bash
#!/bin/bash

LOG_FILE="/var/log/app.log"
LOG_LOCK="/var/log/app.log.lock"

# Atomic log function
log_message() {
    local level=$1
    local message=$2
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S.%N')
    
    # Acquire exclusive lock
    (
        flock -x 200 || exit 1
        
        # Critical section - write log atomically
        echo "$timestamp [$level] $message" >> $LOG_FILE
        
        # Optional: Rotate if too large
        if [ $(stat -f%z $LOG_FILE) -gt 100000000 ]; then
            mv $LOG_FILE "$LOG_FILE.$(date +%s)"
            gzip "$LOG_FILE.*" &
        fi
        
        flock -u 200
    ) 200>$LOG_LOCK
}

# Test with concurrent threads
for i in {1..10}; do
    (
        for j in {1..100}; do
            log_message "INFO" "Thread-$i Message-$j"
        done
    ) &
done

wait
echo "Total log lines: $(wc -l < $LOG_FILE)"
```

**Key Features:**
1. **File lock**: Ensures only one thread writes at a time
2. **Atomic write**: Single echo statement is atomic
3. **Timestamps**: Precise microsecond timestamps
4. **Log rotation**: Prevents disk space issues
5. **Error handling**: Timeout on lock acquisition

**Production Improvements:**
```bash
# Use syslog instead (OS handles synchronization)
logger -t app "Message"

# Or use structured logging with JSON
log_json() {
    local level=$1
    local msg=$2
    local json=$(printf '{"timestamp":"%s","level":"%s","message":"%s"}\n' \
        "$(date -u +%Y-%m-%dT%H:%M:%SZ)" "$level" "$msg")
    
    (
        flock -x 200
        echo "$json" >> $LOG_FILE
        flock -u 200
    ) 200>$LOG_LOCK.json
}

# Or use message queue (asynchronous logging)
# Messages enqueued by threads, dedicated logger thread writes
```

**Monitoring:**
```bash
# Monitor lock contention
watch -n 1 'lsof $LOG_LOCK 2>/dev/null | wc -l'

# Check log file size
du -sh $LOG_FILE

# Verify no corruption
head -5 $LOG_FILE
tail -5 $LOG_FILE
```
</details>

---

## Answer Key Summary

| Question | Answer |
|----------|--------|
| 1 | B |
| 2 | B |
| 3 | B |
| 4 | B |
| 5 | C |
| 6 | C |
| 7 | C |
| 8 | See detailed answer above |
| 9 | See detailed answer above |
| 10 | See detailed answer above |

---

## Scoring Guide

- **9-10 correct**: Excellent understanding of concurrency and thread safety
- **7-8 correct**: Strong knowledge, ready for advanced topics
- **5-6 correct**: Good fundamentals, study synchronization more
- **Below 5**: Review race conditions, mutexes, and deadlock concepts

---
