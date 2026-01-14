# 01 - OS Fundamentals: Solutions

## Solution 1: Identify Your OS and Kernel Version

### Commands

```bash
# Get full OS and kernel information
uname -a

# Get just the OS name
uname -s

# Get just the kernel version
uname -r

# Get processor architecture
uname -m
```

### Expected Output

```
uname -a
Linux mylaptop 5.15.0-91-generic #101-Ubuntu SMP Tue Nov 14 18:34:12 UTC 2023 x86_64 GNU/Linux

uname -s
Linux

uname -r
5.15.0-91-generic

uname -m
x86_64
```

### Explanation

- `Linux` = OS name (kernel)
- `5.15.0` = Kernel version
- `x86_64` = 64-bit architecture (as opposed to i686 for 32-bit)
- The kernel number tells us what version is running; newer is often better for security and performance

---

## Solution 2: Explore the Linux Directory Structure

### Commands

```bash
cd /
ls -la
man hier
```

### Answers

| Directory | Purpose |
|-----------|---------|
| `/bin` | Essential command binaries needed to boot and run the system (e.g., ls, cat, grep) |
| `/home` | User home directories; each user gets a subdirectory here (e.g., /home/username) |
| `/etc` | System configuration files and scripts (e.g., passwd, shadow, fstab) |

### Additional Key Directories

- `/root` = Home directory of the root (superuser)
- `/var` = Variable data files (logs, caches, temp files)
- `/tmp` = Temporary files, cleared on reboot
- `/usr` = User programs and data
- `/lib` = System libraries needed for binaries
- `/opt` = Optional software packages
- `/proc` = Virtual filesystem with process information
- `/sys` = Virtual filesystem with hardware information
- `/dev` = Device files for hardware

---

## Solution 3: Check System Resources

### Commands

```bash
# Check memory
free -h

# Check disk space
df -h

# Get detailed disk usage per partition
df -h | grep -E "^/dev"

# Check specific partition
du -sh /home
```

### Expected Output Example

```
free -h
                total        used        free      shared  buff/cache   available
Mem:           7.7Gi       2.1Gi       1.8Gi       512Mi       3.8Gi       4.9Gi
Swap:          2.0Gi       100Mi       1.9Gi

df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G   20G   27G  42% /
tmpfs           3.9G     0  3.9G   0% /dev/shm
/dev/sda2       100G   45G   51G  46% /home
```

### Explanation

**Memory Report**:
- **Total RAM**: 7.7 GB available
- **Used**: 2.1 GB currently in use
- **Free**: 1.8 GB immediately available
- **Available**: 4.9 GB that can be freed if needed (includes buff/cache)

**Disk Report**:
- **Filesystem**: The physical device or mount point
- **Size**: Total capacity of the partition
- **Used**: Space currently occupied
- **Avail**: Space still available
- **Use%**: Percentage used (critical if approaching 90%)

---

## Solution 4: User and Group Information

### Commands

```bash
whoami
id
groups
id -u  # Just UID
id -g  # Just GID
id -G  # All group IDs
```

### Expected Output Example

```
whoami
john

id
uid=1000(john) gid=1000(john) groups=1000(john),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev)

groups
john adm cdrom sudo dip plugdev
```

### Explanation

- **UID 1000** = Regular user (UID 0 is root)
- **GID 1000** = Primary group
- **Multiple groups** = User can access resources belonging to those groups
- **sudo group** = User can execute commands as root with `sudo`
- **adm group** = User can read system logs

---

## Solution 5: File Permissions Understanding

### Command

```bash
ls -l /etc/passwd
```

### Expected Output

```
-rw-r--r-- 1 root root 2351 Nov 15 10:22 /etc/passwd
```

### Breaking Down the Permissions

```
-rw-r--r-- 1 root root 2351 Nov 15 10:22 /etc/passwd
│││││││││  │ │    │    │    └─ Last modified date
│││││││││  │ │    │    └─ File size in bytes
│││││││││  │ │    └─ Group owner
│││││││││  │ └─ User owner
│││││││││  └─ Link count
││││││││└─ Other permissions (read)
│││││││└─ Other permissions (write = no)
││││││└─ Other permissions (execute = no)
│││││└─ Group permissions (read)
││││└─ Group permissions (write = no)
│││└─ Group permissions (execute = no)
││└─ User permissions (write)
│└─ User permissions (read)
└─ File type (- = regular file)
```

### Summary Table

| Component | Value | Meaning |
|-----------|-------|---------|
| File Type | `-` | Regular file |
| Owner Permissions | `rw-` | Read + Write (6 in octal) |
| Group Permissions | `r--` | Read only (4 in octal) |
| Other Permissions | `r--` | Read only (4 in octal) |
| Owner | `root` | Only root can modify this file |
| Group | `root` | root group can only read |
| Size | `2351` | File is 2351 bytes |

---

## Solution 6: Uptime and Load Average

### Commands

```bash
uptime
nproc
cat /proc/cpuinfo | grep processor | wc -l
top -bn1 | head -n 1
```

### Expected Output Example

```
uptime
 14:32:10 up 45 days, 3:15, 2 users, load average: 0.45, 0.52, 0.48
```

### Explanation

**Time Components**:
- **14:32:10** = Current time
- **up 45 days, 3:15** = System has been running for 45 days, 3 hours, 15 minutes
- **2 users** = 2 users currently logged in
- **load average: 0.45, 0.52, 0.48** = Average CPU load for last 1, 5, 15 minutes

**Load Average Interpretation**:
- Load of **1.0 on 4-CPU system** = 25% CPU utilization (1 core fully used)
- Load of **4.0 on 4-CPU system** = 100% CPU utilization (all cores busy)
- If load > CPU count = System is stressed and tasks are waiting

**For DevOps**:
- Monitor load trending over time
- Alert if load consistently > number of CPUs
- Use for capacity planning and scaling decisions

---

## Solution 7: Process Information

### Commands

```bash
# List all processes
ps aux

# Filter for root processes
ps aux | grep root

# Count processes
ps aux | wc -l

# Get process count excluding headers
ps aux | wc -l | awk '{print $1-1}'

# Show only specific columns
ps aux --sort=-%cpu | head -10
```

### Expected Output Example

```
ps aux | grep root | head -5
root         1  0.0  0.1  77840 11072 ?  Ss   09:17   0:02 /sbin/init
root        10  0.0  0.0  40144  3412 ?  S    09:17   0:00 /lib/systemd/systemd-journald
root        25  0.0  0.0  26748  2848 ?  Ss   09:17   0:00 /lib/systemd/systemd-udevd
root       123  0.0  0.0  35888  3004 ?  Ss   09:17   0:00 /lib/systemd/systemd-logind
root       145  0.0  0.0   4752  2048 ?  Ss   09:17   0:00 /sbin/agetty --autologin root
```

### Explanation

| Column | Meaning |
|--------|---------|
| USER | Process owner |
| PID | Process ID |
| %CPU | CPU usage percentage |
| %MEM | Memory usage percentage |
| VSZ | Virtual memory size |
| RSS | Resident set size (actual memory) |
| STAT | Process state (S=sleeping, R=running, Z=zombie) |
| COMMAND | Command that started the process |

**Root Processes**: Critical system services run as root (init, systemd, udev) for hardware access.

---

## Solution 8: Virtual Memory and Swap

### Commands

```bash
# Check swap usage
free -h
free -h | grep Swap

# Detailed swap information
swapon --show

# Check if swap is being used
vmstat 1 2
```

### Expected Output Example

```
free -h
                total        used        free      shared  buff/cache   available
Mem:           7.7Gi       4.2Gi       1.1Gi       256Mi       2.4Gi       3.1Gi
Swap:          2.0Gi       512Mi       1.5Gi

swapon --show
NAME      TYPE        SIZE   USED PRIO
/swapfile file       2.0Gi 512Mi   -2
```

### Explanation

**Swap Space**:
- **Swap**: Disk space acting as virtual RAM when physical RAM is full
- **Used Swap**: 512 MB being used means physical RAM is under pressure
- **Critical**: If swap usage grows rapidly, system is slowing down (disk is slower than RAM)

**When Swap is Used**:
- Physical RAM is full
- OS moves less-used memory pages to disk
- Performance degrades significantly (disk I/O is slow)

**For DevOps**:
- Monitor swap usage; high swap = system is under stress
- Alert if swap > 50% used
- Increase RAM or optimize applications if swap usage is high

---

## Solution 9: System Information Gathering

### Commands

```bash
# Create report
echo "=== System Info ===" > system_report.txt
uname -a >> system_report.txt
echo "=== CPU Info ===" >> system_report.txt
lscpu >> system_report.txt
echo "=== Memory Info ===" >> system_report.txt
free -h >> system_report.txt
echo "=== Disk Info ===" >> system_report.txt
df -h >> system_report.txt

# View report
cat system_report.txt

# For DevOps, also capture
lsblk  # Block devices (disks)
dmidecode  # Hardware info (requires sudo)
```

### Why This Matters for DevOps

1. **Capacity Planning**: Know current resource usage to predict when scaling is needed
2. **Performance Baseline**: Establish normal metrics for alerting on anomalies
3. **Troubleshooting**: Quick reference when diagnosing performance issues
4. **Infrastructure as Code**: Document hardware specs for disaster recovery
5. **Compliance**: Track resource allocation for billing and license audits

### Real-World Scenario

```
If a server shows:
- 90% disk usage → Action: Clean up logs, add storage
- High CPU load → Action: Investigate processes, optimize app, add CPU
- Swap usage > 50% → Action: Add RAM immediately
- Load average > CPU count → Action: Investigate slow processes
```

---

## Solution 10: Understanding System Calls and Kernel Mode

### Research Answers

#### 1. What is a System Call?

A system call is a request from a user-level process to the kernel to perform a privileged operation. The kernel provides a controlled interface for programs to:
- Access hardware
- Manage memory
- Perform I/O operations
- Create processes
- Control file systems

#### 2. Three Examples of System Calls

1. **`open()`** - Open a file
2. **`read()`** - Read from a file descriptor
3. **`fork()`** - Create a new process
4. **`write()`** - Write to a file
5. **`exit()`** - Terminate a process
6. **`socket()`** - Create a network socket

#### 3. Why Processes Need System Calls

```
User Programs cannot directly:
✗ Access disk hardware
✗ Allocate kernel memory
✗ Create new processes
✗ Access network interfaces
✗ Access other users' memory

System calls provide safe, controlled access!
```

#### 4. What Happens During a System Call

```
Step 1: User Process (User Mode)
   └─> Calls system call function (e.g., open())

Step 2: Trap to Kernel
   └─> CPU switches to Kernel Mode
   └─> CPU saves user program state
   └─> Transfer control to kernel handler

Step 3: Kernel Execution (Kernel Mode)
   └─> Validate request from user
   └─> Access hardware (disk, memory, network)
   └─> Perform requested operation

Step 4: Return to User
   └─> Kernel saves result/error code
   └─> Switch back to User Mode
   └─> Resume user program

This switching is called a "context switch"
```

#### 5. File Reading Scenario

```
User Program wants to read /etc/passwd:

1. User Mode: App calls open("/etc/passwd", O_RDONLY)
2. Trap: CPU switches to Kernel Mode
3. Kernel Checks:
   - Does /etc/passwd exist?
   - Does user have read permission?
4. Kernel Opens File:
   - Allocates file descriptor
   - Updates kernel data structures
5. Return: File descriptor (e.g., 3) sent back to user
6. User Mode: App continues with the file descriptor

7. User Mode: App calls read(3, buffer, 1024)
8. Trap: CPU switches to Kernel Mode
9. Kernel Reads Data:
   - Reads 1024 bytes from disk
   - Copies to user buffer
   - Updates file offset
10. Return: Number of bytes read (e.g., 1024)
11. User Mode: App has data and continues
```

### Commands to Explore System Calls

```bash
# Trace system calls made by a process
strace ls /etc

# See system call made by a running process (requires root)
sudo cat /proc/[PID]/syscall

# Get count of system calls
strace -c ls /etc | tail -20
```

### Expected Output

```bash
strace -c ls /etc | tail -20

% time     seconds  usecs/call     calls    errors name
------ ----------- ----------- --------- --------- ----------------
 22.15    0.001234        21.3        58           brk
 18.42    0.001027        17.1        60           mmap
 15.67    0.000873        14.5        60        1  mprotect
  ...
```

This shows `ls` made 60+ system calls to list files!

---
