# 01 - OS Fundamentals: Cheatsheet

## Essential Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `uname -a` | Show complete OS and kernel info | `uname -a` → Linux host 5.15.0-91-generic |
| `uname -m` | Show system architecture (32/64-bit) | `uname -m` → x86_64 (64-bit) |
| `uname -r` | Show kernel release version | `uname -r` → 5.15.0-91-generic |
| `uname -s` | Show kernel name | `uname -s` → Linux |
| `whoami` | Display current username | `whoami` → john |
| `id` | Show UID, GID, and group memberships | `id` → uid=1000(john) gid=1000(john) groups=... |
| `uptime` | Show system uptime and load average | `uptime` → up 45 days, load average: 0.45, 0.52 |
| `free -h` | Show memory usage (human-readable) | `free -h` → Mem: 7.7Gi used: 2.1Gi |
| `free -m` | Show memory in megabytes | `free -m` → Mem: 7875 used: 2150 |
| `df -h` | Show disk usage by partition | `df -h` → /dev/sda1 50G 20G 27G 42% / |
| `df -i` | Show inode usage | `df -i` → Shows inode statistics |
| `du -sh /path` | Show directory size | `du -sh /home` → 45G /home |
| `lscpu` | Display CPU architecture info | `lscpu` → Shows cores, threads, cache info |
| `nproc` | Show number of CPU cores | `nproc` → 4 |
| `ps aux` | List all running processes | `ps aux \| grep bash` → Find bash processes |
| `ps -ef` | List processes in forest format | `ps -ef --forest` → Shows parent-child relationships |
| `top` | Real-time process monitoring | `top` → Interactive process viewer |
| `htop` | Enhanced process viewer (if installed) | `htop` → Colorized, user-friendly |
| `vmstat 1 2` | Show virtual memory statistics | `vmstat 1 2` → Memory and swap stats |
| `iostat` | Show disk I/O statistics | `iostat` → Disk read/write rates |
| `ls -l` | List files with permissions | `ls -l /etc/passwd` → Shows file details |
| `chmod` | Change file permissions | `chmod 644 file.txt` → rw-r--r-- |
| `chown user:group file` | Change file owner | `chown john:users file.txt` |
| `sudo` | Execute as superuser/root | `sudo apt update` → Run with admin privileges |
| `su - username` | Switch user | `su - john` → Become john user |
| `sudo -l` | List sudoer permissions | `sudo -l` → Show what you can sudo |
| `swapon --show` | Show swap space usage | `swapon --show` → Details of swap partitions |
| `lsblk` | List block devices (disks) | `lsblk` → Disk partition layout |
| `mount` | Show mounted filesystems | `mount` → All mounted partitions |
| `cat /proc/cpuinfo` | Show detailed CPU info | `cat /proc/cpuinfo` → CPU details for each core |
| `cat /proc/meminfo` | Show detailed memory info | `cat /proc/meminfo` → Detailed memory breakdown |
| `man hier` | Documentation of directory structure | `man hier` → Learn about / filesystem |
| `which command` | Find path to command | `which ls` → /bin/ls |
| `strace -c cmd` | Count system calls | `strace -c ls /etc` → System call summary |

---

## File Permission Notation

### Octal Notation

```
Permissio | Octal | Binary | Meaning
----------|-------|--------|----------
r (read)  |   4   | 100    | Can read
w (write) |   2   | 010    | Can modify
x (exec)  |   1   | 001    | Can execute
- (none)  |   0   | 000    | No permission
```

### Examples

```
755 = rwxr-xr-x = Owner: read+write+execute, Group/Others: read+execute
644 = rw-r--r-- = Owner: read+write, Group/Others: read-only
700 = rwx------ = Owner only access
777 = rwxrwxrwx = Everyone has full access (dangerous!)
```

---

## Key Linux Directories

```
/           Root directory (top of filesystem)
/bin        Essential command binaries
/sbin       System binaries (mostly for root)
/home       User home directories
/root       Root user home directory
/etc        System configuration files
/tmp        Temporary files (cleared on reboot)
/var        Variable data (logs, caches)
/var/log    System log files
/usr        User programs and libraries
/lib        System libraries
/opt        Optional software packages
/proc       Virtual filesystem with process info
/sys        Virtual filesystem with hardware info
/dev        Device files
/boot       Boot files and kernel
/media      Mount points for removable media
```

---

## File Permission Structure

```
-rw-r--r--  1  root  root  2351  Nov 15 10:22  /etc/passwd
│││││││││  │  │     │      │      │             │
│││││││││  │  │     │      │      └─ Timestamp  └─ Filename
│││││││││  │  │     │      └─ File size (bytes)
│││││││││  │  │     └─ Group owner
│││││││││  │  └─ User owner
│││││││││  └─ Link count
││││││││└─ Others: execute
│││││││└─ Others: write
││││││└─ Others: read
│││││└─ Group: execute
││││└─ Group: write
│││└─ Group: read
││└─ Owner: execute
│└─ Owner: write
└─ Owner: read (also: - = file, d = directory, l = symlink)
```

---

## Process States

```
R (Running)    = Process is currently executing
S (Sleeping)   = Process is waiting for something (I/O, timer, etc.)
T (Stopped)    = Process is stopped (suspended)
Z (Zombie)     = Process terminated but parent hasn't reaped it
D (Disk sleep) = Process in uninterruptible sleep (waiting for I/O)
```

---

## Load Average Interpretation

```
System with 4 CPUs:

Load 1.0  → 25% CPU utilized (1 core busy)
Load 2.0  → 50% CPU utilized (2 cores busy)
Load 4.0  → 100% CPU utilized (all cores busy)
Load 8.0  → 200% utilized (processes waiting for CPU)

Rule: If load > number of CPUs, system is stressed!

From 'uptime' output:
load average: 0.45, 0.52, 0.48
             ↑     ↑    ↑
             1 min 5 min 15 min averages
```

---

## Memory Units Conversion

```
1 KB  = 1,024 bytes
1 MB  = 1,024 KB = 1,048,576 bytes
1 GB  = 1,024 MB = 1,073,741,824 bytes
1 TB  = 1,024 GB

Commands show human-readable sizes with -h flag:
free -h  → Shows Mi, Gi
df -h    → Shows K, M, G
du -sh   → Shows human-readable size
```

---

## Privilege Levels

```
User Mode (Ring 3)
├─ Limited hardware access
├─ Cannot directly access memory
├─ Cannot perform I/O operations
└─ Can call system calls for kernel services

Kernel Mode (Ring 0)
├─ Full hardware access
├─ Can read/write any memory
├─ Direct I/O control
└─ Provides services to user mode via system calls
```

---

## Quick Diagnostics

```bash
# Check if system is healthy
uptime                  # Load average normal?
free -h                 # Any swap usage? (bad sign)
df -h                   # Any partition > 90%? (dangerous)
ps aux | wc -l         # Too many processes?
vmstat 1 2             # High I/O wait?

# Common troubleshooting sequence
top                     # What's using resources?
ps aux | grep [name]   # Find specific process
kill -9 [PID]          # Force kill if needed
df -h                   # Disk full?
free -h                 # Memory exhausted?
dmesg | tail           # Hardware errors?
```

---

## Common User Groups and Permissions

```
root (UID 0)    = System administrator
wheel/sudo      = Users who can use sudo
adm             = System log access
audio           = Sound device access
video           = Graphics device access
docker          = Docker daemon access (if installed)
```

---

## System Call Categories

```
Process Management: fork(), exec(), exit(), wait()
Memory Management: brk(), mmap(), munmap(), mprotect()
File I/O: open(), read(), write(), close(), lseek()
Directory: mkdir(), rmdir(), chdir(), getcwd()
File Control: chmod(), chown(), stat(), unlink()
Device Control: ioctl(), fcntl()
Networking: socket(), bind(), listen(), accept(), connect()
Signals: signal(), kill(), pause()
Timing: sleep(), alarm(), gettime(), timer_create()
```

---

## Useful One-liners

```bash
# Find largest directories
du -sh /* | sort -h | tail -10

# Monitor memory in real-time
watch -n 1 'free -h'

# Show processes sorted by memory
ps aux --sort=-%mem | head -10

# Show processes sorted by CPU
ps aux --sort=-%cpu | head -10

# Find files modified in last 24 hours
find /home -type f -mtime -1

# Get total number of files in directory
find /path -type f | wc -l

# Check which files are open by a process
lsof -p [PID]

# List all listening ports
ss -tlnp

# Show disk I/O in real-time
iotop
```

---
