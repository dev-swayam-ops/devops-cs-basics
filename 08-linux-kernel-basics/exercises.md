# 08 - Linux Kernel Basics: Exercises

1. **Kernel Version**: `uname -r` - What's your kernel? What do version numbers mean?

2. **Boot Process**: Research: BIOS → Bootloader → Kernel → Init. Stages and time for each?

3. **Kernel Modules**: `lsmod` shows loaded. `modprobe module_name` to load. Why modules instead of monolithic?

4. **dmesg Logs**: `dmesg` shows kernel messages. Find: boot messages, errors, hardware detection.

5. **Kernel Parameters**: `cat /proc/sys/vm/swappiness` - Tune kernel behavior without restart.

6. **System Calls**: `strace ls` - See syscalls. Count calls: `strace -c ls /etc`

7. **Kernel Ring Buffers**: Limited buffer size. `dmesg -e` shows human timestamps. Can they wrap around?

8. **EBPF Programs**: Modern kernel feature. Research and understand kernel-space tracing.

9. **Kernel Panics**: What causes them? How to debug? `echo "panic: test" | kexec_load`

10. **Custom Kernel**: When would DevOps compile custom kernel? Security/performance trade-offs?

---

## Solutions

**Q1**: `5.15.0-91-generic` = Major.Minor.Patch-Release

**Q2**: Bootloader loads kernel, kernel mounts root fs, starts init process (systemd)

**Q3**: Modules loaded on demand, saves memory vs monolithic kernel always in RAM

**Q4**: dmesg shows hardware detection, drivers loading, errors, kernel warnings

**Q5**: `/proc/sys/` allows runtime tuning without reboot (e.g., swappiness)

**Q6**: Syscalls shown by strace; count with `strace -c`. ls makes ~60 syscalls.

**Q7**: Ring buffer has fixed size. Old messages wrap. `dmesg -n7` to filter warnings/errors

**Q8**: eBPF allows user-space control of kernel-space tracing (observability)

**Q9**: OOM killer, memory corruption, driver bugs cause panics. Debug with serial console/kdump

**Q10**: Custom kernel for security patches, old hardware support, security features (SELinux)

---

## Cheatsheet

```bash
uname -a              # Full kernel info
uname -r              # Release version
cat /proc/version     # Kernel version details
lsmod                 # Loaded modules
modprobe module       # Load module
rmmod module          # Unload module
dmesg                 # Kernel messages
dmesg -T              # Kernel messages with time
dmesg -n 7            # Filter level
/proc/sys/            # Kernel parameters
sysctl parameter      # View/set parameters
systool               # Kernel module info
cat /proc/interrupts  # IRQ statistics
cat /proc/iomem       # Memory map
```

## Quiz

**Q1**: Linux kernel runs in?
A) User mode  B) **Kernel mode** C) Ring 3 D) Application space

**Q2**: Bootloader loads what?
A) Init  B) **Kernel** C) systemd D) Application

**Q3**: Modules vs monolithic?
A) Same thing  B) **Modules load on demand** C) Monolithic faster D) Same speed

**Q4**: dmesg shows?
A) User logs  B) **Kernel messages** C) Application errors D) Hardware list

**Q5**: /proc/sys used for?
A) Running processes  B) **Kernel parameters** C) File info D) Process info

**Q6**: strace shows?
A) Memory trace  B) **System calls** C) CPU usage D) I/O operations

**Q7**: What is eBPF?
A) File format  B) **Kernel tracing** C) Boot format D) Ethernet protocol

**Short Answers**:
- **Q8**: What happens during boot? (BIOS → Bootloader → Kernel → Init)
- **Q9**: When customize kernel? (Security patches, old hardware, features)
- **Q10**: Design kernel monitoring (Watch dmesg, check panics, monitor modules)

---
