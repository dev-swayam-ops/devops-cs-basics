# 04 - Memory Management: Exercises

## Exercise 1: Memory Layout (Easy)

Research and draw the memory layout of a typical process:

```bash
# On a running process, check memory sections
cat /proc/self/maps
```

1. What are the main memory segments?
2. What is in each segment?
3. Which grows upward? Which downward?

---

## Exercise 2: Check Memory Usage (Easy)

```bash
# Start memory-intensive process
python3 -c "x = [0]*10000000; import time; time.sleep(100)" &
PID=$!

# Check memory
ps aux | grep $PID
free -h
top -p $PID
```

1. What is VSZ (Virtual Size)?
2. What is RSS (Resident Set)?
3. Why might VSZ > RSS?

---

## Exercise 3: Memory Allocation Patterns (Easy)

Track memory as you allocate more:

```bash
#!/bin/bash
echo "PID: $$"
echo "Initial RSS:"
cat /proc/$$/status | grep VmRSS

# Allocate 100MB
python3 << 'EOF'
import os
x = [0] * (100 * 1024 * 1024 // 8)
os.system('cat /proc/$$/status | grep VmRSS')
EOF
```

1. How much did RSS increase?
2. Is all allocated memory used?
3. What is the difference between allocated and used?

---

## Exercise 4: Memory Limits (Medium)

Test memory limits:

```bash
# Check current limits
ulimit -a
ulimit -v  # Virtual memory

# Set limit to 500MB
ulimit -v 500000

# Try to allocate more
python3 -c "x = [0]*1000000000"
```

1. What happened?
2. Why use memory limits?
3. How would you set limits for a service?

---

## Exercise 5: Swap Usage and Performance (Medium)

Monitor swap behavior:

```bash
# Check swap before
free -h

# Allocate more than physical RAM
python3 << 'EOF'
import time
# Request more memory than available
total_mb = 8000  # Adjust based on your RAM
x = [0] * (total_mb * 1024 * 1024 // 8)
time.sleep(30)
EOF

# Check in another terminal
free -h
vmstat 1 10
```

1. When does swap get used?
2. What happens to performance?
3. How can you detect swap usage issues?

---

## Exercise 6: Page Faults (Medium)

Understand page faults:

```bash
# Run program with strace to see page faults
strace -c ls /etc

# Check output for "mmap" calls (page mapping)
# Check for "brk" calls (heap extension)
```

1. What is a page fault?
2. What is the difference between major and minor faults?
3. How do page faults affect performance?

---

## Exercise 7: Memory Leaks Detection (Medium)

Create and detect a memory leak:

```bash
#!/bin/bash
# Program that leaks memory

python3 << 'EOF'
import gc
import sys

def leak_memory():
    big_list = []
    for i in range(1000000):
        # Append object that references itself (circular)
        obj = {'data': [0]*1000, 'ref': None}
        obj['ref'] = obj  # Circular reference
        big_list.append(obj)
    # Function exits but objects not freed

# Check memory before
import subprocess
pid = os.getpid()
subprocess.run(['bash', '-c', f'cat /proc/{pid}/status | grep VmRSS'])

leak_memory()

# Check after (memory not freed)
subprocess.run(['bash', '-c', f'cat /proc/{pid}/status | grep VmRSS'])
EOF
```

1. What is a memory leak?
2. How did you detect it?
3. How would you fix it?

---

## Exercise 8: Cache and Memory Efficiency (Medium)

Understand cache impact:

```bash
# Sequential access (cache-friendly)
python3 << 'EOF'
import time
import numpy as np

# Large array
data = np.arange(100 * 1024 * 1024)

# Sequential access (cache hits)
start = time.time()
sum1 = sum(data)
seq_time = time.time() - start

# Random access (cache misses)
np.random.shuffle(data)
start = time.time()
sum2 = sum(data)
random_time = time.time() - start

print(f"Sequential: {seq_time:.3f}s")
print(f"Random: {random_time:.3f}s")
EOF
```

1. Why is sequential access faster?
2. What is cache locality?
3. How can you optimize memory access?

---

## Exercise 9: Memory Protection and Segmentation (Medium)

Test memory protection:

```bash
# This should fail - accessing invalid memory
bash -c 'exec 999>&-; cat /proc/$$/mem' 2>&1

# Check memory sections and permissions
cat /proc/$$/maps | head -10

# Try to write to read-only segment (should fail)
python3 -c "import ctypes; ctypes.string_at(0, 10)" 2>&1
```

1. What is memory protection?
2. Why is it important?
3. What happens when you access unallocated memory?

---

## Exercise 10: Memory Profiling (Medium)

Profile memory usage of a real program:

```bash
# Using memory_profiler if installed
pip install memory-profiler

# Create test program
cat > test.py << 'EOF'
from memory_profiler import profile

@profile
def allocate():
    x = [0] * 10000000
    y = [0] * 20000000
    return len(x) + len(y)

allocate()
EOF

python -m memory_profiler test.py
```

1. What does the profiler show?
2. Where is most memory used?
3. How would you optimize?

---
