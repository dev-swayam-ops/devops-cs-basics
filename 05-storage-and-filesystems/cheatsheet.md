# 05 - Storage and Filesystems: Cheatsheet & Quiz

## Cheatsheet - Filesystem Commands

| Command | What It Does | Example Output |
|---------|-------------|-----------------|
| `df -h` | Show free disk space | `Filesystem Size Used Avail /dev/sda1 50G 20G 27G` |
| `du -sh *` | Show directory sizes | `4.5G dir1, 2.3G dir2` |
| `mount` | List mounted filesystems | `/dev/sda1 on / type ext4 (rw,relatime)` |
| `lsblk` | List block devices | `sda├─sda1, ├─sda2, └─sda3` |
| `stat file` | File metadata | `Size: 1024, Inode: 123456` |
| `ln file link` | Create hard link | Same inode, both files point to same data |
| `ln -s file link` | Create symlink | Points to filename, not inode |
| `find / -size +1G` | Find large files | Finds all files >1GB |
| `tar czf out.tgz dir` | Compress directory | Creates compressed archive |
| `fsck` | Filesystem check | Repairs filesystem errors |

## Memory

```
Filesystem Cache:
- Page Cache: File content caching
- Buffer Cache: Disk block caching
- Dentry Cache: Directory entry caching

All automatic, managed by kernel
```

## Quick Quiz - Answers

**Q1: What's in an inode?**
A: Ownership, permissions, timestamps, size, link count, block pointers

**Q2: Why hard links fail across filesystems?**
A: Inodes unique to filesystem; can't reference inode on different filesystem

**Q3: Symlink vs Hard Link?**
A: Symlink = shortcut to filename; hard link = same inode

**Q4: How to find what's using disk?**
```bash
du -sh ~ /* | sort -rh | head -10
```

**Q5: Backup vs Archive?**
A: Backup = full copy for recovery; Archive = compressed for storage

**Q6: RAID 1 vs RAID 5?**
A: RAID 1 = mirroring (slower writes); RAID 5 = striping+parity (balanced)

**Q7: Check filesystem for errors?**
```bash
sudo fsck -n /dev/sda1  # -n = read-only check
```

---

## Filesystem Performance Tuning

```bash
# Check filesystem options
tune2fs -l /dev/sda1 | grep -i feature

# Disable access time updates (improves performance)
mount -o remount,noatime /

# Check I/O scheduler
cat /sys/block/sda/queue/scheduler

# Change scheduler (cfq, deadline, noop)
echo deadline > /sys/block/sda/queue/scheduler
```

## Common Filesystem Issues

| Problem | Symptom | Solution |
|---------|---------|----------|
| Disk full | `No space left` | `du -sh`, delete old logs |
| Slow I/O | High wait time | Check `iostat`, optimize queries |
| Corrupted | Can't mount | `fsck -y` to repair |
| Inodes full | Despite space free | Delete many small files |
| Permission denied | Can't access files | Check with `ls -l`, use `chmod` |

---
