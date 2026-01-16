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

The operating system separates execution into two distinct privilege levels to ensure system stability and security.

**Kernel Mode**
- The CPU executes with full access to all hardware resources (memory, disk, I/O devices)
- Only the kernel runs in this mode - the core of the OS responsible for managing all system resources
- Privileged operations like memory management, device control, and interrupt handling happen here
- When CPU is in kernel mode, it can execute special privileged instructions
- Example: Allocating memory pages, switching between processes, handling hardware interrupts

**User Mode**
- Regular applications and user processes run with restricted access to hardware
- Cannot directly access hardware; must go through the kernel for privileged operations
- Provides isolation - if one application crashes, it doesn't affect others or the kernel
- Makes systems more secure and stable

**System Calls**
- The mechanism that bridges kernel and user mode
- When a user application needs kernel service (like reading a file), it makes a "system call"
- This triggers a mode switch from user to kernel mode - an expensive operation
- Examples: `open()`, `read()`, `write()`, `fork()`, `exit()` are all system calls
- The context switch overhead is why minimizing system calls is important for performance

#### Practical Examples:

**Example 1: Pure User Mode (No Kernel Involved)**
```c
int a = 10, b = 20;
int c = a + b;
```
- This arithmetic operation runs entirely in user mode
- Only uses CPU registers and process memory
- No kernel interaction needed
- No mode switch occurs

**Example 2: User Mode → Kernel Mode (System Call)**
```c
printf("Hello\n");  // Looks simple, but internally:
write(1, "Hello\n", 6);  // This is a system call to kernel
```
- `printf()` internally calls `write()` system call
- CPU switches from user mode → kernel mode → back to user mode
- Kernel's terminal driver handles the actual output
- User code cannot directly access terminal hardware

**Example 3: Multiple System Calls (File I/O)**
```c
fd = open("data.txt", O_RDONLY);   // System call #1
read(fd, buffer, 100);              // System call #2
close(fd);                          // System call #3
```
- Each line triggers a user mode → kernel mode → user mode cycle
- `open()`: Kernel checks permissions and initializes file descriptor
- `read()`: Kernel controls disk I/O operations
- `close()`: Kernel releases file resources
- These context switches have measurable performance cost - why buffering reads/writes is important

**Typical 64-bit Linux systems expose 300+ system calls (often a few hundred, depending on kernel version and CPU architecture)**

### 2. Process Basics

A process is the fundamental unit of execution in an operating system. Each process is an independent running instance of a program.

**Process (Process Image)**
- The complete runtime environment of a program: code, data, stack, and resources
- Each process has its own memory space - one process cannot directly access another's memory
- Identified uniquely by its Process ID (PID)
- Contains the program's executable code, current data values, and execution state

**Process ID (PID)**
- A unique positive integer assigned by the kernel to each process
- Remains constant throughout the process's lifetime
- When a process terminates, its PID can be reassigned to a new process
- Used by the OS to track and manage processes
- Example: Process 1234, Process 5678, etc.

**Parent Process (PPID)**
- Every process (except the first) is created by another process called its parent
- The parent's PID is stored as PPID in the child process
- Processes form a tree structure with `init`(old) (PID 1) or systemd (modern Linux) at the root
- Useful for understanding process relationships and cleanup (when parent dies, children are orphaned)

**Process State**
- **Running (R)**: Currently executing on the CPU
- **Sleeping/Waiting (S)**: Waiting for an event (I/O, signal, etc.) - interruptible
- **Uninterruptible Sleep (D)**: Waiting for I/O that cannot be interrupted
- **Zombie (Z)**: Terminated but parent hasn't read its exit status yet so waits for wait()
- **Orphan process**: child process whose parent has terminated, but the child is still running.
- **Stopped (T)**: Suspended via signal, waiting to be resumed
- Processes transition between states throughout their lifetime based on events and scheduling

**When you run a program, a user process (shell) requests the kernel via system calls (fork/clone + execve). The kernel creates the process, assigns PID, sets up memory/resources, then starts the program in user mode.**

### 3. System Resources

The OS manages several critical hardware resources that all processes need to share fairly.

**CPU (Processor)**
- The hardware component that executes instructions from processes
- Modern systems have multiple CPU cores allowing true parallel execution
- Limited resource: only so much computation can happen simultaneously
- The scheduler decides which process gets CPU time and for how long
- CPU-bound tasks (calculations) compete heavily for this resource

*Example: CPU-bound Task (Continuous Computation)*
```bash
python3 -c "while True: pass"
```
This infinite loop keeps the CPU busy with heavy computation. Monitor with `top` to see high CPU usage (100%) and minimal memory/I/O activity. Running multiple copies causes all processes to compete for available CPU cores.

**Memory (RAM)**
- Volatile storage where the OS and all running processes keep code and data
- Limited resource: you can't run more processes than memory allows
- Each process gets its own address space - kernel protects processes from accessing each other's memory
- Divided into pages (typically 4KB) by the memory management system
- RAM is much faster than disk, which is why efficient memory use matters

*Example: Memory-hungry Task (Large Allocation)*
```bash
python3 -c "a = 'x' * (500 * 1024 * 1024); input('Allocated 500MB RAM... Press Enter to exit')"
```
This allocates ~500MB of RAM and holds it. Check memory usage with `top` or `free -h`. Running multiple copies can exhaust available RAM, forcing the OS to use slower disk swap space or trigger an OOM (Out Of Memory) killer.

**I/O (Input/Output)**
- Includes disk storage, network cards, USB devices, terminals, printers
- Much slower than CPU and memory - a key bottleneck
- Processes often block (wait) while I/O completes - this is normal
- The kernel manages I/O to prevent conflicts (two processes can't write to same file simultaneously)
- Examples: Reading from disk, sending data over network, keyboard input

*Example: Disk I/O Task (Heavy Reading)*
```bash
cat /path/to/largefile.txt > /dev/null
```
This reads a large file from disk and discards output. During execution, the CPU mostly waits (blocks) while disk I/O is pending. Monitor with `iostat` to see high disk I/O. The bottleneck is disk speed, not CPU speed.

**Scheduling**
- The algorithm that decides which process runs on the CPU at any moment
- Fair allocation: each process should get a reasonable share of CPU time
- Context switching: the CPU rapidly switches between processes (hundreds of times per second)
- Preemption: the kernel can forcibly pause a process to let another run
- Without scheduling, one process could monopolize the CPU and freeze all others

*Example: Two Processes Competing for CPU*

Terminal 1:
```bash
python3 -c "while True: pass"
```

Terminal 2:
```bash
python3 -c "while True: pass"
```

Both processes continuously demand the CPU. The Linux scheduler rapidly switches between them (time slicing), giving each a fair share of processing time. Run `top` to see both processes sharing CPU cores, keeping the system responsive. This demonstrates how scheduling prevents one process from starving others.

### 4. File System

The file system is how the OS organizes and provides access to persistent data on disk.

**Hierarchical Structure**
- Files and directories organized in a tree starting from root directory `/`
- Everything in Linux is under `/` - there's no separate drive letters like Windows
- Typical structure: `/home`, `/var`, `/etc`, `/bin`, `/usr`, etc.
- Absolute paths start with `/`; relative paths are relative to current directory
- Allows logical organization of thousands of files

**Inodes**
- The data structure that stores metadata about each file (not the content)
- Contains: file size, owner, permissions, timestamps (created, modified, accessed), file type
- Each file is identified by an inode number on its filesystem
- The filename is just a human-readable reference to the inode number
- Commands like `ls -i` show inode numbers

**Permissions**
- Three levels of permission: read (r=4), write (w=2), execute (x=1)
- Applied to three categories: owner (user), group, others
- Combined as three digits: owner permissions + group permissions + others permissions
- Example: `rwxr-xr-x` = 755 = owner can read/write/execute, group and others can only read/execute
- Execute permission on a directory means you can enter it; on a file means you can run it

**Ownership**
- Every file has an owner (user) and group
- Only the owner or root can change permissions
- Groups allow multiple users to share access to files
- Example: A file owned by `sharu:devops` means user sharu owns it, devops group has group permissions

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


