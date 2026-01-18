# 08 - Linux Kernel Basics

## What You'll Learn

- Kernel architecture and purpose
- System calls (syscalls)
- Kernel vs user space
- Process states and context switching
- Interrupt handling
- Kernel modules
- dmesg logs and kernel messages
- Kernel parameters and tuning

## Prerequisites

- Completed **07-dns-and-http-basics**
- Understanding of processes and OS basics
- Familiarity with Linux command line

## Key Concepts

### 1. Kernel Architecture

**User Space**: Applications (web servers, containers, tools). Cannot directly access hardware. Syscalls are only interface to kernel.

**Kernel Space**: Privileged mode with full hardware access. Single kernel per system, shared by all processes.

**Mode Switch**: User → Kernel expensive (~100+ CPU cycles). Understand this when optimizing syscall-heavy workloads.

**Key Subsystems DevOps Cares About**:
- **Process Management** - scheduling, signals
- **Memory Management** - paging, swaps
- **Networking** - TCP/IP, firewall (iptables)
- **Device Drivers** - hardware abstraction

**DevOps Relevance**: Know where performance bottlenecks hide. High syscall rates = performance drag.

### 2. System Calls (Syscalls)

**What**: User programs request kernel services through syscalls. Transition from user mode → kernel mode → user mode.

**Cost**: ~100-500 CPU cycles per syscall (mode switch, context, TLB flush, cache misses).

**Common Syscalls**:
- **Process**: `fork()`, `exec()`, `exit()`, `wait()`
- **File**: `open()`, `read()`, `write()`, `close()`
- **Memory**: `mmap()`, `brk()`
- **Signals**: `signal()`, `kill()`

**Optimization**: Reduce syscalls in hot paths. Batch I/O operations. Use async I/O to avoid blocking syscalls.

**Example**: Reading 1KB file with 1000 reads = 1000 syscalls (expensive). One `read()` for full file = 1 syscall.

**DevOps**: Monitor with `strace -c` to find syscall bottlenecks in applications.

### 3. Process States and Context Switching

**Key States**:
- **Ready**: Waiting for CPU (thousands can be ready)
- **Running**: Executing on CPU (one per core)
- **Blocked**: Waiting for I/O/event (doesn't consume CPU)
- **Zombie**: Exited but parent hasn't called `wait()` (memory freed, small process table entry remains)

**Context Switch Overhead**: ~1000-10000 CPU cycles. Saves/restores registers, flushes TLB, invalidates cache.

**Problem**: Too many processes = thrashing (system spends time switching, not working).

**DevOps Metrics to Monitor**:
- `ps aux | wc -l` - too many processes?
- `vmstat` - context switches per second (high = thrashing)
- Check zombie processes: `ps aux | grep Z` (indicates parent bug)

**Rule of Thumb**: Keep running processes ≤ 2× CPU cores for good performance.

### 4. Interrupts and IRQ Handling

**Hardware Interrupts**: External events (keyboard press, disk I/O complete, network packet). CPU pauses process, runs interrupt handler, resumes.

**Software Interrupts**: Triggered by code (syscalls, divide-by-zero, page fault, exceptions).

**Interrupt Handler**: Kernel code that runs immediately. Must be **very fast** (no blocking, no I/O). Often just acknowledges interrupt, schedules deferred work.

**Interrupt Overhead**: Hundreds-thousands per second on busy systems. Each causes context save/restore.

**Problem**: Interrupt storms (broken device generating thousands/sec) = system unresponsive.

**Monitor**: `cat /proc/interrupts` - check for abnormal IRQ rates. `watch -n 1 'cat /proc/interrupts'` - watch for spikes.

**DevOps Action**: High interrupt count on specific IRQ? Investigate driver or hardware issue.

### 5. Kernel Modules and Parameters

**Kernel Modules**: Runtime-loadable kernel extensions. Drivers, filesystems, protocol handlers. Stored in `/lib/modules/$(uname -r)/`.

**Commands**:
- `modprobe driver_name` - load + handle dependencies
- `rmmod driver_name` - unload (fails if in use)
- `lsmod` - list loaded modules
- `dmesg` - check load errors

**Common Modules**: Network (`e1000`, `ixgbe`), storage (`ahci`), filesystems (`ext4`, `xfs`).

**Kernel Parameters**: Tunable settings in `/proc/sys/` or `/etc/sysctl.conf`.

**Important Parameters for DevOps**:
- `kernel.shmmax` - max shared memory (databases)
- `net.core.somaxconn` - backlog for connections
- `net.ipv4.tcp_max_syn_backlog` - SYN flood protection
- `fs.file-max` - max open files per system
- `kernel.pid_max` - max process IDs

**Check**: `sysctl -a`. **Change**: `sysctl -w kernel.param=value`. **Persist**: Edit `/etc/sysctl.conf` + `sysctl -p`.

**DevOps**: Database tuning often requires adjusting shmmax, ulimits. Web servers need tcp_max_syn_backlog tuning.

## Hands-on Lab: Kernel Exploration

### Lab Overview
Examine kernel messages, modules, and parameters.

### Lab Commands

```bash
# 1. Show kernel version
uname -a

# Expected:
# Linux myhost 5.10.0 #1 SMP x86_64 GNU/Linux

# 2. Kernel release
uname -r

# Expected: 5.10.0 or similar

# 3. List kernel modules
lsmod | head

# Expected:
# Module  Size  Used by
# overlay 65536 2
# ext4    614400 3

# 4. Load module (if you have permission)
sudo modprobe module_name

# Expected: (no output means success)

# 5. Unload module
sudo modprobe -r module_name

# Expected: (or error if in use)

# 6. Kernel messages
dmesg | head -20

# Expected:
# [0.000000] Linux version 5.10...
# [0.000000] Command line: BOOT_IMAGE=...

# 7. Recent kernel errors
dmesg | grep -i error

# Expected: (any errors, if any)

# 8. Kernel parameters
sysctl -a | head

# Expected:
# kernel.pid_max = 4194304
# kernel.shmmax = 68719476736

# 9. Change kernel parameter (temporarily)
sudo sysctl -w kernel.shmmax=1073741824

# Expected: kernel.shmmax = 1073741824

# 10. Check /proc/sys directly
cat /proc/sys/kernel/pid_max

# Expected: 4194304 (max PID)
```

## Validation

```bash
# Can you check kernel version?
uname -a && echo "✓ Kernel version shown"

# See kernel modules?
lsmod && echo "✓ Modules listed"

# View kernel messages?
dmesg | head && echo "✓ Messages visible"

# Access kernel parameters?
sysctl kernel.pid_max && echo "✓ Parameters shown"
```

## Cleanup

No cleanup needed (read-only except for sysctl changes).

## Common Mistakes

1. **User vs kernel**: User space can't directly access hardware
2. **Syscall overhead**: Each syscall switches mode (expensive)
3. **Context switch**: Process scheduling has overhead
4. **Modules permanent**: Changes lost on reboot (use /etc/sysctl.conf)
5. **Interrupt handlers**: Must be fast (don't do I/O)

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Module won't load | Check: lsmod, dmesg for errors |
| Parameter won't change | Check: permissions, sysctl.conf |
| Kernel panic | Check: dmesg, hardware issues |
| High interrupts | Check: irq usage, interrupt storm |
| Memory issues | Check: kernel.shmmax, limits |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for commands
4. Move to **09-system-calls-and-signals** after completion

## Additional Resources

- Kernel: `man 7 kernel`, `man uname`
- Syscalls: `man 2 syscalls`
- Modules: `man modprobe`, `man lsmod`
- Parameters: `man sysctl`, `man 5 sysctl.conf`

