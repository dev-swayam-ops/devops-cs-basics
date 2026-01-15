# 08 - Linux Kernel Basics Cheatsheet

## Kernel Information Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `uname -a` | Full kernel info | Version, arch, hostname |
| `uname -r` | Kernel release | 5.10.0-13-generic |
| `uname -m` | Machine type | x86_64, arm64 |
| `uname -n` | Hostname | myhost |
| `hostnamectl` | System hostname | Modern alternative |

## Kernel Message Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `dmesg` | Kernel messages | Boot log, errors |
| `dmesg -T` | Readable timestamps | Human-readable time |
| `dmesg | grep -i error` | Find errors | Error messages |
| `dmesg | tail -20` | Recent messages | Last 20 lines |
| `journalctl -b` | Boot messages | systemd journal |

## Kernel Module Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `lsmod` | List modules | All loaded drivers |
| `modprobe module` | Load module | Dynamic loading |
| `modprobe -r module` | Unload module | Remove from kernel |
| `modinfo module` | Module details | Info about driver |
| `grep module /proc/modules` | Check if loaded | Is module active |

## Kernel Parameter Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `sysctl -a` | All parameters | 1000+ values |
| `sysctl kernel.pid_max` | Get parameter | Single value |
| `sysctl -w kernel.shmmax=1GB` | Set parameter | Temporary change |
| `cat /proc/sys/kernel/pid_max` | Direct read | Same as sysctl |
| `echo 1 > /proc/sys/vm/drop_caches` | Write parameter | Dangerous, admin only |

## CPU Information Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `cat /proc/cpuinfo` | CPU details | All processor info |
| `nproc` | Core count | Number of CPUs |
| `lscpu` | CPU details | Cleaner format |
| `cat /proc/cpuinfo \| grep processor` | Count processors | How many total |
| `grep "cpu MHz" /proc/cpuinfo` | CPU speed | Current frequency |

## Memory Information Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `cat /proc/meminfo` | Memory details | All memory stats |
| `free -h` | Quick memory | Used/free/total |
| `cat /proc/sys/kernel/shmmax` | Max shared mem | Large value |
| `cat /proc/sys/kernel/shmmni` | Shared segments | Max number |
| `sysctl vm.swappiness` | Swap preference | 0-100 value |

## System Limits Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ulimit -a` | User limits | All limits |
| `ulimit -n` | File descriptors | Open files |
| `ulimit -u` | Processes | Max processes |
| `cat /proc/sys/fs/file-max` | System file limit | Global limit |
| `sysctl kernel.threads-max` | Thread limit | Max threads |

## Common Kernel Parameters

| Parameter | Purpose | Typical Value |
|-----------|---------|----------------|
| kernel.pid_max | Max process ID | 4194304 |
| kernel.shmmax | Max shared memory | 68719476736 |
| vm.swappiness | Swap preference | 60 |
| net.core.somaxconn | Connection backlog | 128 |
| fs.file-max | Global open files | 2097152 |
| kernel.threads-max | Max threads | 1032768 |

## /proc Filesystem Reference

| File | Purpose | Content |
|------|---------|---------|
| /proc/cpuinfo | CPU information | Processor details |
| /proc/meminfo | Memory information | RAM statistics |
| /proc/cmdline | Kernel boot args | Command line |
| /proc/modules | Loaded modules | Same as lsmod |
| /proc/uptime | System uptime | Seconds up, idle |
| /proc/loadavg | Load average | 1, 5, 15 min |
| /proc/version | Kernel version | Full version string |
| /proc/sys/ | Kernel parameters | Tunable values |

## Kernel Version Scheme

| Part | Example | Meaning |
|------|---------|---------|
| Major | 5 | API/compatibility |
| Minor | 10 | Features added |
| Patch | 0 | Bug fixes |
| Build | 13 | Distribution build |

## Common Modules

| Module | Purpose | Example |
|--------|---------|---------|
| ext4 | Filesystem | Disk storage |
| overlay | Container FS | Docker/containers |
| bluetooth | Wireless | BT devices |
| usbhid | USB input | Keyboard/mouse |
| iwlwifi | WiFi driver | Intel wireless |

## Kernel Tuning (/etc/sysctl.conf)

```bash
# Edit to make permanent
sudo nano /etc/sysctl.conf

# Add lines like:
kernel.pid_max = 4194304
vm.swappiness = 10
net.core.somaxconn = 1024

# Apply changes
sudo sysctl -p
```

## Troubleshooting Kernel Issues

| Issue | Check | Command |
|-------|-------|---------|
| Module won't load | Dependencies | lsmod, dmesg |
| Out of memory | Limits | free, sysctl |
| Many processes | Limits | ps, sysctl kernel.pid_max |
| Slow system | Swap usage | free -h, sysctl vm.swappiness |
| Driver missing | Loaded modules | lsmod, grep |

## Performance Tuning Tips

```bash
# Check current settings
sysctl vm.swappiness          # Lower = less swap

# Disable swap (if RAM enough)
sysctl -w vm.swappiness = 0

# Increase file limit
sysctl -w fs.file-max = 2097152

# Optimize TCP
sysctl -w net.core.somaxconn = 4096

# Drop cache (safe)
sync; echo 3 > /proc/sys/vm/drop_caches
```

## Quick Diagnostics

```bash
# System overview
uname -a; free -h; nproc

# Check errors
dmesg | grep -i error

# Module status
lsmod | head

# Key parameters
sysctl kernel.pid_max kernel.shmmax vm.swappiness
```
