# 01 - OS Fundamentals Exercises

## Easy Exercises (1-5)

### Exercise 1: Check OS Version
**Objective**: Identify your operating system and kernel version.

**Task**: 
- Run a command to display OS name and kernel version
- Identify the architecture (x86_64, ARM, etc.)
- What Linux distribution are you using?

---

### Exercise 2: Monitor System Resources
**Objective**: Understand current system usage.

**Task**:
- Check current memory usage (used vs free)
- Check available disk space on root partition
- What percentage of memory is currently used?
- How much free disk space do you have?

---

### Exercise 3: Explore Processes
**Objective**: List and understand running processes.

**Task**:
- List all running processes
- Identify the process with the highest CPU usage
- Identify the process with the highest memory usage
- How many processes are currently running?

---

### Exercise 4: Check System Uptime and Load
**Objective**: Understand system uptime and load average.

**Task**:
- How long has the system been running?
- What are the 1-minute, 5-minute, and 15-minute load averages?
- Is the system under heavy load? (Compare load to CPU count)
- How many users are currently logged in?

---

### Exercise 5: View File Permissions
**Objective**: Understand file permissions and ownership.

**Task**:
- List /etc/passwd file with permissions
- Who owns the file?
- What permissions do you (user) have?
- What permissions do others have?
- Convert the permission bits to octal (e.g., 644)

---

## Medium Exercises (6-10)

### Exercise 6: Analyze CPU Information
**Objective**: Understand CPU configuration.

**Task**:
- Display detailed CPU information
- How many CPU cores do you have?
- What is the CPU model name?
- What is the maximum CPU frequency?
- Are all cores running at the same speed?

---

### Exercise 7: Monitor System Load Over Time
**Objective**: Understand load average context.

**Task**:
- Check the current load average
- Count how many CPU cores you have
- Is load average > CPU count? (indicates waiting processes)
- Run `uptime` multiple times to see changes
- What conclusion can you draw about system load?

---

### Exercise 8: Explore Process Hierarchy
**Objective**: Understand parent-child process relationships.

**Task**:
- Find your current shell process (bash, zsh, etc.)
- What is its PID?
- What is its PPID (parent)?
- What other processes share the same parent?
- What is the ultimate parent (init with PID 1)?

---

### Exercise 9: Check Memory Layout
**Objective**: Understand memory usage metrics.

**Task**:
- Display total system memory
- Calculate: Used / Total * 100 = % used
- What is the difference between "used" and "buff/cache"?
- What is "available" memory vs "free" memory?
- Run the command before and after opening an application - what changed?

---

### Exercise 10: Investigate Filesystem Hierarchy
**Objective**: Understand filesystem structure.

**Task**:
- Display mounted filesystems
- What is mounted at /? (root partition)
- What is mounted at /home? (if different from /)
- What filesystems are virtual (/proc, /sys)?
- Identify which partition is growing fastest (compare % used)

---

## Submission Tips

- Run each command in your terminal
- Copy the output and understand what each field means
- Compare your results with the solutions
- Try variations: `ps aux`, `ps -ef`, `ps -o pid,user,cmd`
- Explore man pages: `man ps`, `man df`, `man free`
