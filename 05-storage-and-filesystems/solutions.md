# 05 - Storage and Filesystems: Solutions & Cheatsheet

## Quick Solutions

### Solution 1: Filesystem Exploration

Hard links and symbolic links are key filesystem concepts.

| Aspect | Hard Link | Symlink |
|--------|-----------|---------|
| **Definition** | Direct reference to inode | Reference to filename |
| **Inode** | Same inode as original | Different inode |
| **Across filesystems** | No | Yes |
| **Delete original** | Content still accessible | Broken link |
| **Size** | Same as original | Small (filename size) |
| **Command** | `ln file link` | `ln -s file link` |

### Solution 2: Inode Structure

An inode stores:
- Ownership (UID/GID)
- Permissions
- File size
- Timestamps (modified, accessed, changed)
- Link count
- Pointers to data blocks

### Solution 3: File Permissions

```
Octal 600 = rw------- (owner: read+write, others: none)
Octal 644 = rw-r--r-- (owner: read+write, others: read)
Octal 755 = rwxr-xr-x (owner: full, others: read+execute)
```

---

## Filesystem Commands Cheatsheet

| Command | Purpose | Example |
|---------|---------|---------|
| `df -h` | Disk space by filesystem | `df -h /home` |
| `du -sh` | Directory size | `du -sh ~` |
| `mount` | Show mounted filesystems | `mount \| grep sda` |
| `lsblk` | Show block devices | `lsblk -a` |
| `fdisk -l` | List disk partitions | `sudo fdisk -l` |
| `mkfs` | Create filesystem | `sudo mkfs -t ext4 /dev/sda1` |
| `fsck` | Check filesystem | `sudo fsck /dev/sda1` |
| `tune2fs` | Modify filesystem | `tune2fs -l /dev/sda1` |
| `ln` | Create hard link | `ln file link` |
| `ln -s` | Create symlink | `ln -s file link` |
| `stat` | File details | `stat filename` |
| `find` | Search files | `find / -name "*.log"` |
| `tar` | Archive files | `tar czf arch.tar.gz dir` |
| `gzip` | Compress | `gzip file` |
| `rsync` | Sync files | `rsync -avz src/ dst/` |

---

## Filesystem Types

```
ext4: Linux standard, journaling, supports 16TB
XFS: High-performance, large files, journaling
Btrfs: Copy-on-write, snapshots, RAID
ZFS: Advanced, checksums, compression
NFS: Network filesystem
SMB/CIFS: Windows networking
tmpfs: RAM-based, fast, temporary
```

---

## RAID Levels

```
RAID 0: Striping (speed, no redundancy)
RAID 1: Mirroring (redundancy, no speed gain)
RAID 5: Striping + Parity (balance)
RAID 6: Striping + Dual Parity
RAID 10: Striping + Mirroring
```

---

## Quiz

### Question: Difference between hard and soft links?

**Answer**: Hard link points to same inode; symlink points to filename. Hard link survives deletion; symlink breaks.

### Question: How to find large files?

**Answer**: `find / -size +100M` or `du -sh /* | sort -rh`

### Question: Backup strategy?

**Answer**: 3-2-1 rule: 3 copies, 2 media types, 1 offsite

---
