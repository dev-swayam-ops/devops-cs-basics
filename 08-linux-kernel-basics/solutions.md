# 08 - Linux Kernel Basics Solutions

## Easy Solutions (1-5)

### Solution 1: Check Kernel Version

**Command**:
```bash
uname -a
```

**Output Example**:
```
Linux myhost 5.10.0-13-generic #1 SMP Debian 5.10.0-13 (2024-12-03) x86_64 GNU/Linux
```

**Breakdown**:
- Kernel: Linux
- Version: 5.10.0 (major.minor.patch)
- Processor: x86_64 (Intel/AMD 64-bit)
- OS: GNU/Linux (Debian)

---

### Solution 2: List Kernel Modules

**Command**:
```bash
lsmod | head -10
```

**Output Example**:
```
Module                  Size  Used by
overlay                65536  2
ext4                  614400  3
mbcache                16384  1 ext4
jbd2                  131072  1 ext4
crc32c_generic         16384  1 jbd2
```

**Analysis**:
- overlay: 65KB, used by 2 modules
- ext4: filesystem driver
- jbd2: journaling (used by ext4)
- Dependencies: shown in "Used by"

---

### Solution 3: View Kernel Messages

**Command**:
```bash
dmesg | head -20
```

**Output Example**:
```
[0.000000] Linux version 5.10.0 (GNU/Linux)
[0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz root=/dev/sda1
[0.000000] KERNEL supported cpus:
[0.000000]   Intel GenuineIntel
[0.000000]   AMD AuthenticAMD
[0.001000] x86/fpu: Supporting XSAVE feature 0x001: 'x87 floating point registers'
[0.001000] x86/fpu: Supporting XSAVE feature 0x002: 'SSE registers'
[0.010000] Memory: 8142788K/8299900K available
```

**Information**:
- Boot parameters
- CPU detected
- Memory size: ~8GB
- Timestamps: seconds since boot

---

### Solution 4: Check Process Limits

**Commands**:
```bash
sysctl kernel.pid_max
sysctl kernel.shmmax
```

**Output Example**:
```
kernel.pid_max = 4194304
kernel.shmmax = 68719476736
```

**Explanation**:
- pid_max: ~4 million processes (large)
- shmmax: ~64GB shared memory (system dependent)
- Why limits: prevent resource exhaustion

---

### Solution 5: View Kernel Parameters

**Command**:
```bash
sysctl -a | head -20
```

**Output Example**:
```
abi.vsyscall32 = 1
crypto.fips_enabled = 0
debug.exception-trace = 1
kernel.acpi_video_flags = 0
kernel.panic = 0
kernel.pid_max = 4194304
```

**Parameter Types**:
- kernel.*: core kernel settings
- fs.*: filesystem parameters
- vm.*: virtual memory
- net.*: network settings

---

## Medium Solutions (6-10)

### Solution 6: Find Errors in dmesg

**Command**:
```bash
dmesg | grep -iE "(error|warn|fail)" | head
```

**Output Example**:
```
[2.345678] WARNING: CPU0: Package temperature/speed normal
[3.456789] systemd[1]: Failed to mount /dev/shm.
```

**Analysis**:
- Type: WARNING vs error
- Module: systemd, CPU
- Severity: often non-critical
- Check timestamp: how long ago

---

### Solution 7: Check CPU Information

**Commands**:
```bash
cat /proc/cpuinfo
nproc
```

**Output Example**:
```
processor       : 0
vendor_id       : GenuineIntel
cpu family      : 6
model           : 158
stepping        : 10
microcode       : 0x98
cpu MHz         : 3599.904
cache size      : 8192 KB
cores           : 4
siblings        : 8
```

**Analysis**:
- Processors: 8 listed (0-7)
- Cores: 4 (physical)
- Siblings: 8 (with hyperthreading)
- Speed: ~3.6 GHz

---

### Solution 8: Understand Module Dependencies

**Command**:
```bash
lsmod | grep ext4
modinfo ext4 2>/dev/null || echo "Not available"
```

**Output Example**:
```
ext4  614400  3
ext4 depends on: crc32c_generic, jbd2, mbcache
description: Fourth Extended Filesystem
```

**Dependencies**:
- ext4 needs: jbd2 (journaling), crc32c (checksums)
- Removing ext4 requires removing dependents
- Can't unload if in use

---

### Solution 9: Check System Memory Limits

**Commands**:
```bash
sysctl vm.swappiness
sysctl kernel.shmmax
free -h
```

**Output Example**:
```
vm.swappiness = 60
kernel.shmmax = 68719476736

Mem:           15Gi
Swap:          2.0Gi
```

**Analysis**:
- Swappiness: 60 (moderate swap preference)
- Shared mem max: 64GB
- Actual RAM: 15GB
- Swap: 2GB

---

### Solution 10: Compare Kernel Versions

**Commands**:
```bash
uname -r
# Output: 5.10.0-13-generic
```

**Versioning**:
- Major: 5 (API compatibility)
- Minor: 10 (features)
- Patch: 0 (bug fixes)
- Build: 13 (distribution number)

**Comparison**:
- 5.15 > 5.10 (newer)
- 5.10.5 > 5.10.0 (newer patch)
- 6.x > 5.x (much newer)

---

## Quick Reference

```bash
# Kernel info
uname -a; uname -r

# Modules
lsmod; lsmod | grep module

# Messages
dmesg | head
dmesg | grep -i error

# Parameters
sysctl kernel.pid_max
sysctl -a | grep vm.

# CPU
cat /proc/cpuinfo
nproc

# Memory
free -h
cat /proc/meminfo
```
