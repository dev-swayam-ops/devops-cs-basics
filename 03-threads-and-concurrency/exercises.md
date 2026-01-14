# 03 - Threads and Concurrency: Exercises

## Exercise 1: Understand Threading Basics (Easy)

Research and define:
1. What is a thread?
2. How do threads differ from processes?
3. What is multithreading?
4. Name 3 advantages of using threads

---

## Exercise 2: Identify Threads in Running Processes (Easy)

```bash
ps -eLf | grep bash
ps -eLf | grep systemd
pthreads_demo &
ps -eLf | grep pthreads_demo
cat /proc/[PID]/status | grep Threads
```

1. How many threads does your bash process have?
2. How many threads does systemd have?
3. What command shows thread count?

---

## Exercise 3: Race Condition Scenario (Easy)

Create a simple race condition:

```bash
#!/bin/bash
counter=0
for i in {1..100}; do
    ((counter++)) &
done
wait
echo "Counter: $counter"
echo "Expected: 100"
```

1. Why might counter be less than 100?
2. What is a race condition?
3. How does this relate to threads?

---

## Exercise 4: Thread Safety (Medium)

Analyze this code for thread safety issues:

```bash
# Shared resource
FILE="/tmp/shared.txt"
echo "0" > $FILE

# Multiple threads trying to increment
for i in {1..10}; do
    {
        value=$(cat $FILE)
        sleep 0.1  # Simulate processing
        echo $((value + 1)) > $FILE
    } &
done
wait

echo "Final value: $(cat $FILE)"
```

1. What is thread safety?
2. Why is this code NOT thread-safe?
3. How would you fix it?
4. What is a mutex/lock?

---

## Exercise 5: Mutual Exclusion with flock (Medium)

Implement thread-safe file access:

```bash
FILE="/tmp/shared.txt"
LOCK="/tmp/shared.lock"

# Safe increment function
safe_increment() {
    exec 200> $LOCK
    flock 200
    value=$(cat $FILE)
    echo $((value + 1)) > $FILE
    flock -u 200
}

echo "0" > $FILE
for i in {1..10}; do
    safe_increment &
done
wait

echo "Final value: $(cat $FILE)"
```

1. What does `flock` do?
2. Run the test and verify the final value
3. Compare with non-locked version

---

## Exercise 6: Deadlock Scenario (Medium)

Create a deadlock example:

```bash
# Resource A and B
touch /tmp/resource_a
touch /tmp/resource_b

# Thread 1: Lock A, then try to lock B
(
    exec 300>/tmp/resource_a
    flock 300
    echo "Thread 1: Locked A"
    sleep 2
    echo "Thread 1: Trying to lock B..."
    exec 301>/tmp/resource_b
    flock 301
    echo "Thread 1: Locked B"
) &

# Thread 2: Lock B, then try to lock A
(
    exec 301>/tmp/resource_b
    flock 301
    echo "Thread 2: Locked B"
    sleep 2
    echo "Thread 2: Trying to lock A..."
    exec 300>/tmp/resource_a
    flock 300
    echo "Thread 2: Locked A"
) &

wait
```

1. Run this and observe what happens
2. What is a deadlock?
3. How can you prevent deadlocks?

---

## Exercise 7: Thread Pool Concept (Medium)

Implement a simple thread pool pattern:

```bash
#!/bin/bash
POOL_SIZE=4
WORK_QUEUE="/tmp/work_queue"
RESULTS="/tmp/results"

# Create queue
rm -f $WORK_QUEUE
mkfifo $WORK_QUEUE

# Worker function
worker() {
    while read task; do
        echo "Processing: $task"
        sleep 1
        echo "Completed: $task" >> $RESULTS
    done < $WORK_QUEUE
}

# Start workers
for i in $(seq 1 $POOL_SIZE); do
    worker &
done

# Submit tasks
for task in task1 task2 task3 task4 task5; do
    echo $task > $WORK_QUEUE &
done

wait
cat $RESULTS
```

1. What is a thread pool?
2. How many workers are processing tasks?
3. How does this improve performance?

---

## Exercise 8: Semaphore Pattern (Medium)

Implement resource limiting with semaphores:

```bash
#!/bin/bash
# Only 2 threads can access resource at once

SEMAPHORE="/tmp/sem"
MAX_PERMITS=2

# Initialize
for i in $(seq 1 $MAX_PERMITS); do
    echo "available" >> $SEMAPHORE
done

# Acquire semaphore
acquire() {
    flock 300
    if [ $(wc -l < $SEMAPHORE) -gt 0 ]; then
        head -n1 $SEMAPHORE | tail -n+2 > $SEMAPHORE.tmp
        mv $SEMAPHORE.tmp $SEMAPHORE
        flock -u 300
        return 0
    fi
    flock -u 300
    return 1
}

# Release semaphore
release() {
    flock 300
    echo "available" >> $SEMAPHORE
    flock -u 300
}

# Use resource
use_resource() {
    echo "Task $1: Entering critical section"
    sleep 2
    echo "Task $1: Exiting critical section"
}

# Submit tasks
for i in {1..5}; do
    (
        acquire
        use_resource $i
        release
    ) &
done

wait
```

1. What is a semaphore?
2. How does this limit concurrent access?
3. Why is this useful?

---

## Exercise 9: Producer-Consumer Problem (Medium)

Implement classic concurrency problem:

```bash
#!/bin/bash
BUFFER_SIZE=5
BUFFER="/tmp/buffer"

# Initialize buffer
echo "" > $BUFFER

# Producer
producer() {
    for i in {1..10}; do
        # Wait if buffer is full
        while [ $(wc -l < $BUFFER) -gt $BUFFER_SIZE ]; do
            sleep 0.1
        done
        
        echo "Item-$i" >> $BUFFER
        echo "Produced: Item-$i"
        sleep $((RANDOM % 3))
    done
}

# Consumer
consumer() {
    for i in {1..10}; do
        # Wait if buffer is empty
        while [ ! -s $BUFFER ]; do
            sleep 0.1
        done
        
        item=$(head -n1 $BUFFER)
        tail -n+2 $BUFFER > $BUFFER.tmp
        mv $BUFFER.tmp $BUFFER
        echo "Consumed: $item"
        sleep $((RANDOM % 3))
    done
}

producer &
consumer &

wait
```

1. What is the producer-consumer problem?
2. How does the buffer control flow?
3. What could go wrong in this implementation?

---

## Exercise 10: Concurrency Issues in Real Code (Medium)

Analyze this multi-threaded scenario:

```bash
#!/bin/bash
# Web request handling

LOG="/tmp/requests.log"

# Simulate web request handler
handle_request() {
    request_id=$1
    
    # Log request (NOT THREAD-SAFE!)
    echo "Request-$request_id starting" >> $LOG
    
    # Simulate processing
    sleep 1
    
    # Update counter (NOT THREAD-SAFE!)
    count=$(cat /tmp/request_count 2>/dev/null || echo 0)
    echo $((count + 1)) > /tmp/request_count
    
    echo "Request-$request_id completed" >> $LOG
}

# Handle multiple concurrent requests
echo "0" > /tmp/request_count
for i in {1..10}; do
    handle_request $i &
done

wait
echo "Total requests processed: $(cat /tmp/request_count)"
```

1. What thread safety issues exist?
2. Rewrite this to be thread-safe
3. How would you handle this in production?
4. What monitoring would you add?

---
