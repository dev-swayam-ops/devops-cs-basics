# 08 - Linux Kernel Basics Exercises

## Easy Exercises (1-5)

### Exercise 1: Check Kernel Version
**Objective**: Know your kernel.

**Task**:
- Run: `uname -a`
- What's kernel version?
- What's hardware? (x86_64, arm?)
- How many processors?
- When was it built? (date in output?)

---

### Exercise 2: List Kernel Modules
**Objective**: See loaded drivers.

**Task**:
- Run: `lsmod | head -10`
- Which modules loaded?
- What's "Size"? (memory)
- What's "Used by"? (dependencies)
- Find a module you recognize

---

### Exercise 3: View Kernel Messages
**Objective**: Understand boot process.

**Task**:
- Run: `dmesg | head -20`
- What's first message? (kernel version)
- What's CPU info?
- What's memory info? (RAM detected)
- What about errors? (usually none)

---

### Exercise 4: Check Process Limits
**Objective**: Understand kernel limits.

**Task**:
- Run: `sysctl kernel.pid_max`
- What's max PID? (usually 4194304)
- Run: `sysctl kernel.shmmax`
- What's max shared memory?
- Why limits needed?

---

### Exercise 5: View Kernel Parameters
**Objective**: Explore configuration.

**Task**:
- Run: `sysctl -a | grep -E "^\w+\..*=" | head`
- How many parameters?
- What's difference: kernel.* vs fs.* vs vm.*?
- Pick one, understand what it does

---

## Medium Exercises (6-10)

### Exercise 6: Find Errors in dmesg
**Objective**: Identify problems.

**Task**:
- Run: `dmesg | grep -iE "(error|warn|fail)"`
- Any errors found?
- If yes, what type? (driver, firmware?)
- How long ago? (timestamp)
- Are they critical?

---

### Exercise 7: Check CPU Information
**Objective**: Analyze processor details.

**Task**:
- Run: `cat /proc/cpuinfo`
- How many cores?
- What's CPU model?
- What's processor speed? (GHz)
- Run: `nproc` for count

---

### Exercise 8: Understand Module Dependencies
**Objective**: See how modules relate.

**Task**:
- Run: `lsmod`
- Pick a module with "Used by" count
- What modules depend on it?
- Try: `modinfo module_name` (if available)
- What's purpose of module?

---

### Exercise 9: Check System Memory Limits
**Objective**: Know kernel memory settings.

**Task**:
- Run: `sysctl vm.swappiness`
- What's value? (0-100)
- Run: `sysctl kernel.shmmax`
- What's max shared memory?
- Run: `free -h` (compare)

---

### Exercise 10: Compare Kernel Versions
**Objective**: Understand versioning.

**Task**:
- Run: `uname -r`
- Format: major.minor.patch-build
- What's major version? (5, 4?)
- What does patch number mean?
- Check: which is newer, 5.10 or 5.15?

---

## Submission Tips

1. Use `uname -a` for complete kernel info
2. Use `lsmod` frequently (shows current state)
3. dmesg shows boot messages (scroll for history)
4. sysctl parameters are dynamic
5. Remember: changes lost on reboot unless in /etc/sysctl.conf
