# 08 - Linux Kernel Basics

## What You'll Learn

Master Linux kernel fundamentals:
- Kernel architecture and subsystems
- Kernel modules and device drivers
- System calls and syscall interface
- Kernel logging and dmesg
- Kernel parameters and tuning
- Performance profiling
- Kernel upgrades and management

## Prerequisites

- Completed **07-dns-and-http-basics**
- Understanding of Linux processes and memory
- Comfort with command-line administration

## Key Concepts

### 1. Kernel Subsystems
- **Process Scheduler**: Allocates CPU time
- **Memory Manager**: Virtual memory, paging
- **I/O Manager**: Disk and device access
- **Network Stack**: TCP/IP protocol handling
- **Filesystem**: File storage and access
- **Security**: Permissions, SELinux, AppArmor

### 2. Kernel Modules
- Loadable code into kernel
- Extend kernel without recompiling
- Device drivers often implemented as modules
- Can be loaded/unloaded dynamically

### 3. System Calls
- Bridge between user space and kernel
- Examples: open(), read(), write(), fork(), exit()
- Context switch between user and kernel mode
- Expensive operation (performance cost)

### 4. Kernel Logging
- dmesg buffer holds boot and runtime messages
- Persisted to /var/log/kern.log
- Ring buffer, old messages overwritten
- Critical for hardware and driver debugging

## Hands-on Lab: Kernel Exploration

### Lab Steps

```bash
# 1. Check kernel version
uname -r
uname -a
cat /proc/version

# 2. View kernel messages
dmesg | tail -20
dmesg | grep -i error

# 3. Check loaded kernel modules
lsmod
lsmod | wc -l

# 4. Get module information
modinfo ext4
modinfo nf_conntrack

# 5. View kernel parameters
sysctl -a
sysctl kernel.pid_max
sysctl vm.swappiness

# 6. Modify kernel parameter (temporary)
sysctl -w net.core.somaxconn=65535

# 7. Make permanent (add to /etc/sysctl.conf)
echo "net.core.somaxconn=65535" >> /etc/sysctl.conf
sysctl -p

# 8. Check process syscall activity
strace -c ls /tmp
strace -e open,read ls /tmp

# 9. Monitor syscall statistics
syscall-monitor.sh

# 10. View system call table
cat /proc/sys/kernel/
```

## Validation

Verify your kernel knowledge:

```bash
# Can you check kernel version?
uname -r && echo "✓ Kernel version visible"

# Can you view kernel messages?
dmesg | tail && echo "✓ Kernel messages accessible"

# Can you see modules?
lsmod | head && echo "✓ Modules visible"

# Can you modify kernel parameters?
sysctl kernel.pid_max && echo "✓ Kernel parameters accessible"
```

## Cleanup

```bash
# Remove any test modifications
sysctl -w net.core.somaxconn=4096
sed -i '/net.core.somaxconn=65535/d' /etc/sysctl.conf
```

## Common Mistakes

1. **Ring buffer overflow**: dmesg buffer is limited, old messages lost
2. **Kernel module conflicts**: Incompatible modules can panic kernel
3. **Sysctl not persistent**: Changes lost on reboot without sysctl.conf
4. **Syscall overhead**: Minimize syscalls in performance-critical code
5. **Kernel panics**: Don't experiment on production!

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Kernel panic | Check: dmesg, hardware errors, BIOS settings |
| Module won't load | Check: modinfo, dependencies, kernel version match |
| Performance drop | Check: dmesg for errors, syscall tracing, profiling |
| Device not recognized | Check: dmesg for device, load appropriate module |
| Kernel crash on boot | Check: grub config, kernel image, boot parameters |

## Next Steps

1. Move to **09-system-calls-and-signals** for syscall deep dive
2. Learn about kernel profiling (perf, flamegraphs)
3. Study kernel debugging (kdb, kgdb)
4. Explore kernel security (SELinux, AppArmor)
5. Learn about kernel compilation

## Additional Resources

- Kernel documentation: https://www.kernel.org/doc/
- Linux kernel internals: https://www.kernel.org/
- Kernel modules: `man modprobe`
- System calls: `man 2 syscalls`

