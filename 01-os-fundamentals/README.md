# 01 - OS Fundamentals

## What You'll Learn

- Operating system structure and purpose
- Kernel vs user mode
- Process and thread basics
- System resources (CPU, memory, I/O)
- Basic OS administration and monitoring
- System information commands

## Prerequisites

- Linux command line basics (cd, ls, cat)
- Text editor knowledge
- No programming required

## Key Concepts

### 1. Kernel vs User Mode
- **Kernel Mode**: Full hardware access, privileged operations
- **User Mode**: Restricted access, application code runs here
- **System Calls**: Bridge between user and kernel (costly operation)

### 2. Process Basics
- **Process**: Running instance of a program with unique PID
- **Process ID (PID)**: Unique identifier per process
- **Parent Process (PPID)**: Parent's PID
- **State**: Running, waiting, stopped, zombie

### 3. System Resources
- **CPU**: Processor cores executing instructions
- **Memory**: RAM for storing data and code
- **I/O**: Disk, network, terminals
- **Scheduling**: Fair allocation of CPU time

### 4. File System
- Hierarchical structure starting from root (/)
- Inodes store file metadata
- Permissions: read (4), write (2), execute (1)
- Ownership: user, group, others

## Hands-on Lab: OS Exploration

### Lab Overview
Explore your operating system and system resources.

### Lab Commands

```bash
# 1. Get OS information
uname -a

# Expected output:
# Linux ubuntu 5.10.0-8-generic #1 SMP x86_64 GNU/Linux

# 2. Check system uptime
uptime

# Expected output:
# 10:32:45 up 5 days, 3:24, 2 users, load average: 0.15, 0.10, 0.09

# 3. View CPU information
lscpu | head -15

# Expected output:
# Architecture: x86_64
# CPU op-mode(s): 32-bit, 64-bit
# CPU(s): 8

# 4. Check memory usage
free -h

# Expected output:
#              total  used  free  shared  buff/cache  available
# Mem:          15Gi 4.2Gi 2.3Gi   256Mi       8.5Gi        10Gi

# 5. Check disk space
df -h | head -5

# Expected output:
# Filesystem Size Used Avail Use% Mounted on
# /dev/sda1 100G  45G   55G  45% /

# 6. View current user
whoami

# Expected output:
# ubuntu

# 7. List processes
ps aux | head -5

# Expected output:
# USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
# root 1   0.0  0.1  10632  6320 ?  Ss  10:00  0:01 /sbin/init

# 8. Check file permissions
ls -l /etc/passwd

# Expected output:
# -rw-r--r-- 1 root root 1234 Oct 20 10:00 /etc/passwd

# 9. View system load
cat /proc/loadavg

# Expected output:
# 0.15 0.10 0.09 1/234 5678

# 10. Check hostname
hostname

# Expected output:
# ubuntu
```

## Validation

Verify your understanding:

```bash
# Can you get OS info?
uname -a && echo "✓ OS info accessible"

# Can you check memory?
free -h && echo "✓ Memory check works"

# Can you list processes?
ps aux | wc -l && echo "✓ Processes visible"

# Can you check disk?
df -h && echo "✓ Disk info visible"
```

## Cleanup

No cleanup needed - all commands were read-only.

## Common Mistakes

1. **Confusing PID and PPID**: PID is process's ID, PPID is parent's ID
2. **Memory units**: 1GB = 1024MB (not 1000MB)
3. **Process states**: R=running, S=sleeping, Z=zombie, D=uninterruptible
4. **Permission bits**: Read(4) + Write(2) + Execute(1) = 7
5. **Root directory**: / is root, not /root (which is root's home)

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Command not found | Check PATH: `echo $PATH` |
| Permission denied | Use `sudo` or check file permissions with `ls -l` |
| Can't view file | Check permissions, try `sudo cat filename` |
| Process not found | List all: `ps aux \| grep processname` |
| Memory high | Check: `free -h`, kill unused processes |

## Next Steps

1. Complete the 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for command reference
4. Move to **02-process-management** after completion

## Additional Resources

- Man pages: `man 7 man-pages`
- Linux foundation: https://www.linuxfoundation.org/
- Kernel docs: https://www.kernel.org/doc/

