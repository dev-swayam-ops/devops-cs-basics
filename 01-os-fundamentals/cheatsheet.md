# 01 - OS Fundamentals Cheatsheet

## System Information Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `uname -a` | Full system information | Shows OS, kernel, architecture |
| `uname -r` | Kernel version only | Returns: 5.10.0-8-generic |
| `hostname` | Display system name | Returns: ubuntu |
| `whoami` | Current user | Returns: ubuntu |
| `id` | User ID and groups | Returns: uid=1000(ubuntu) gid=1000(ubuntu) groups=... |

## Resource Monitoring

| Command | Purpose | Example |
|---------|---------|---------|
| `uptime` | System uptime and load | Shows: up 5 days, load average: 0.15, 0.10, 0.09 |
| `free -h` | Memory usage (human readable) | Shows: Mem: 15Gi used 4.2Gi |
| `df -h` | Disk space usage | Shows: /dev/sda1 100G 45G 55G 45% / |
| `lscpu` | CPU information | Shows: Architecture, cores, model name |
| `cat /proc/loadavg` | Load average details | Shows: 0.15 0.10 0.09 1/234 5678 |

## Process Management

| Command | Purpose | Example |
|---------|---------|---------|
| `ps aux` | List all processes | Shows: USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND |
| `ps -ef` | Alternative format | Same as ps aux, different display |
| `pstree` | Process hierarchy tree | Shows parent-child relationships |
| `ps -o pid,user,cmd` | Custom columns | Shows only PID, USER, COMMAND |
| `ps -p $$` | Show current shell | $$ = current PID |

## File Permissions

| Command | Purpose | Example |
|---------|---------|---------|
| `ls -l /etc/passwd` | Show file with permissions | `-rw-r--r-- 1 root root` |
| `stat /etc/passwd` | Detailed file info | Shows: Access: (0644/-rw-r--r--) |
| `stat -c '%a %n' file` | Show octal permissions | Returns: 644 filename |
| `chmod 755 file` | Change permissions | Makes: rwxr-xr-x (owner full, others read+execute) |
| `chmod +x file` | Add execute permission | Makes file executable |

## Advanced Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `top` | Real-time process monitoring | Shows live CPU/memory usage |
| `htop` | Top with better UI | More user-friendly than top |
| `vmstat` | Virtual memory statistics | Shows: r, b, swpd, free, buff, cache |
| `iostat` | Disk I/O statistics | Shows: tps, read/write rates |
| `lscpu \| head -15` | Show CPU details (first 15 lines) | Extract key CPU info |

## Navigation & File Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `cd /` | Go to root directory | Root of filesystem |
| `cd ~` | Go to home directory | User's home folder |
| `pwd` | Print working directory | Show current path |
| `ls -la /` | List root with hidden files | Show all files including hidden |
| `cat /etc/os-release` | Show Linux distribution | Name, version info |

## Output Filtering

| Command | Purpose | Example |
|---------|---------|---------|
| `ps aux \| head -5` | Show first 5 lines | Limit output |
| `ps aux \| wc -l` | Count processes | Total number of processes |
| `ps aux \| grep bash` | Find bash processes | Filter by name |
| `ps aux --sort=-%cpu` | Sort by CPU descending | Highest CPU first |
| `ps aux --sort=-%mem` | Sort by memory descending | Highest memory first |

## Key Concepts Quick Reference

### Process States
- **R** = Running (on CPU or queue)
- **S** = Sleeping (waiting for event)
- **Z** = Zombie (terminated, parent not waited)
- **D** = Disk sleep (uninterruptible, usually I/O)

### Permission Bits
- **4** = Read (r)
- **2** = Write (w)
- **1** = Execute (x)
- **6** = rw (read + write)
- **7** = rwx (all permissions)

### Memory Terminology
- **used** = Actually allocated to processes
- **free** = Completely unused
- **available** = Can be freed if needed (includes cache)
- **buff/cache** = Disk cache (can be cleared)
- **VSZ** = Virtual memory (allocated, may not be in RAM)
- **RSS** = Resident memory (actually in RAM)

### Load Average Formula
- Load = number of processes ready to run
- If load < CPU count: idle time exists
- If load = CPU count: 100% utilization
- If load > CPU count: processes waiting

### Common File Locations
- `/etc/passwd` = User accounts (rw-r--r--)
- `/etc/shadow` = Password hashes (rw-------)
- `/proc/[pid]/` = Process info
- `/sys/` = Kernel configuration
- `/dev/` = Device files
