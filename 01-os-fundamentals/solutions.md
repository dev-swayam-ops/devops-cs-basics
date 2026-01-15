# 01 - OS Fundamentals Solutions

## Easy Solutions (1-5)

### Solution 1: Check OS Version

**Command**:
```bash
uname -a
```

**Output Example**:
```
Linux ubuntu 5.10.0-8-generic #1 SMP x86_64 GNU/Linux
```

**Explanation**:
- `Linux` = OS name
- `ubuntu` = hostname
- `5.10.0-8-generic` = kernel version
- `x86_64` = architecture (64-bit Intel/AMD)

**Answer**:
- Distribution: Check with `cat /etc/os-release` or `lsb_release -d`
- Architecture: x86_64 (64-bit processor)

---

### Solution 2: Monitor System Resources

**Commands**:
```bash
# Memory usage
free -h

# Disk usage
df -h

# Percentage calculation
free -h | grep Mem | awk '{print ($3/$2)*100 "%"}'
```

**Output Example**:
```
              total  used  free
Mem:           15Gi 4.2Gi  2.3Gi
```

**Explanation**:
- Used memory = 4.2Gi out of 15Gi
- Percentage = (4.2 / 15) × 100 = 28%
- Available space for processes = "available" column

---

### Solution 3: Explore Processes

**Commands**:
```bash
# List all processes
ps aux

# Find highest CPU usage
ps aux --sort=-%cpu | head -2

# Find highest memory usage
ps aux --sort=-%mem | head -2

# Count processes
ps aux | wc -l
```

**Explanation**:
- `ps aux` shows all processes with details
- `--sort=-%cpu` sorts by CPU (descending)
- Column %CPU and %MEM show resource usage
- System processes (init, systemd) use minimal resources

---

### Solution 4: Check System Uptime and Load

**Commands**:
```bash
# Check uptime and load
uptime

# Just load average
cat /proc/loadavg
```

**Output Example**:
```
10:32:45 up 5 days, 3:24, 2 users, load average: 0.15, 0.10, 0.09
0.15 0.10 0.09 1/234 5678
```

**Explanation**:
- `up 5 days` = system running 5 days without restart
- Load: 0.15 (1-min), 0.10 (5-min), 0.09 (15-min)
- If load < CPU count: system has idle time
- Load = 0.15 on 8-core CPU = 1.87% utilization (healthy)

---

### Solution 5: View File Permissions

**Commands**:
```bash
# View with permissions
ls -l /etc/passwd

# Decode permissions
stat /etc/passwd

# Convert to octal
stat -c '%a %n' /etc/passwd
```

**Output Example**:
```
-rw-r--r-- 1 root root 1234 Oct 20 10:00 /etc/passwd
644 /etc/passwd
```

**Explanation**:
- `-rw-r--r--` = regular file, owner can read/write, group/others read only
- `644` = rw-(4+2) r--(4) r--(4)
- Owner: read + write = 6
- Group: read = 4
- Others: read = 4

---

## Medium Solutions (6-10)

### Solution 6: Analyze CPU Information

**Commands**:
```bash
# Detailed CPU info
lscpu

# Extract specific values
lscpu | grep "CPU(s):"
lscpu | grep "Model name:"
lscpu | grep "CPU max MHz:"
```

**Output Example**:
```
Architecture:                    x86_64
CPU(s):                          8
Model name:                      Intel(R) Core(TM) i7-9700K
CPU max MHz:                     4900.0000
```

**Explanation**:
- 8 cores available for parallel execution
- Model name identifies processor type
- Max MHz = maximum clock speed (4900 MHz = 4.9 GHz)
- All cores on modern systems support turbo boost

---

### Solution 7: Monitor System Load Over Time

**Commands**:
```bash
# Check load
uptime

# Count CPU cores
nproc

# Check in loop
while true; do uptime; sleep 1; done
```

**Output Example**:
```
load average: 0.15, 0.10, 0.09
8
```

**Analysis**:
- 8 cores available
- Load 0.15 = 0.15/8 = 1.87% utilization
- If load > 8 = waiting processes
- Load 8.0 = 100% CPU utilization
- Load 16.0 = some processes waiting

---

### Solution 8: Explore Process Hierarchy

**Commands**:
```bash
# Find bash process
ps -o pid,ppid,cmd | grep bash

# Show process tree
pstree -p $$

# Get detailed info
ps -p $$ -o pid,ppid,user,cmd
```

**Output Example**:
```
PID  PPID  CMD
2345 1234  bash
```

**Explanation**:
- $$ = current shell's PID
- PPID = terminal emulator or SSH process
- PID 1 = init/systemd (ultimate parent)
- Orphan process gets adopted by init

---

### Solution 9: Check Memory Layout

**Commands**:
```bash
# Full memory info
free -h

# Detailed breakdown
cat /proc/meminfo | head -10

# Calculate percentage
free | awk 'NR==2 {printf "Used: %.1f%%\n", ($3/$2)*100}'
```

**Output Example**:
```
              total   used   free  shared buff/cache available
Mem:          15Gi  4.2Gi  2.3Gi   256Mi     8.5Gi      10Gi
```

**Explanation**:
- `used` = actually used by applications
- `buff/cache` = disk cache (can be freed if needed)
- `available` = freeable memory (includes buff/cache)
- Opening app increases `used` and decreases `free`

---

### Solution 10: Investigate Filesystem Hierarchy

**Commands**:
```bash
# Show mounted filesystems
df -h

# Show with percentages
df -h | sort -k5 -rn

# Find fastest growing
du -sh /* | sort -rh | head -5
```

**Output Example**:
```
Filesystem     Size  Used Avail Use%  Mounted on
/dev/sda1     100G  45G  55G   45%   /
/dev/sdb1     500G  200G 300G  40%   /home
tmpfs         2.0G  0    2.0G  0%    /run
```

**Explanation**:
- `/` = root filesystem (OS and system)
- `/home` = user data (often separate partition)
- `tmpfs` = virtual filesystem (RAM-based, temporary)
- Identify partition closest to capacity for warnings

---

## Tips for Practice

1. Run commands multiple times to see variations
2. Compare output before and after system changes
3. Use `watch` command: `watch -n 1 uptime` (refresh every 1 second)
4. Explore related commands: `top`, `htop`, `vmstat`, `iostat`
5. Man pages: `man free`, `man ps`, `man df`, `man uname`
