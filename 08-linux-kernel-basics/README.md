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
```
User Space
├── Applications
├── Libraries (libc)
└── Shells

System Call Interface

Kernel Space
├── Process Management
├── Memory Management
├── File Systems
├── Device Drivers
├── Networking
└── Hardware
```

### 2. System Calls (syscalls)
- Bridge between user and kernel
- Examples: open, read, write, fork, exec
- Switch to kernel mode (expensive)
- Always go through libc

### 3. Process States
```
New → Ready → Running → Blocked → Zombie → Terminated
      ↑                    ↓
      └────────────────────┘
```

### 4. Interrupts
- Hardware: keyboard, disk, network
- Software: system calls
- Interrupt handler: kernel routine
- Context switch: pause current process

### 5. Kernel Modules
- Dynamic kernel extensions
- Can be loaded/unloaded
- Example: drivers (lsmod shows loaded)

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

