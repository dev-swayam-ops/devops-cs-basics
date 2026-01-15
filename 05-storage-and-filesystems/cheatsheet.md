# 05 - Storage and Filesystems Cheatsheet

## Disk and Partition Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `lsblk` | List block devices | Show partitions and mounts |
| `lsblk -f` | Show filesystem types | ext4, xfs, btrfs, etc |
| `fdisk -l` | Detailed partition info | GPT and MBR details |
| `parted /dev/sda print` | Partition table | Detailed layout |
| `blkid` | UUID and labels | Device identifiers |

## Disk Usage Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `df -h` | Disk free (human readable) | All mounted filesystems |
| `df -i` | Inode usage | Inode percentage |
| `du -sh /path` | Directory size | Total only |
| `du -sh /*` | Top-level sizes | All main dirs |
| `du -sh /path/*` | Subdirectory breakdown | Each subdir size |
| `find / -size +100M` | Find large files | >100MB files |

## File and Permission Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ls -l` | List with permissions | Full details |
| `ls -i` | Show inode numbers | Inode column |
| `stat /path` | File metadata | All file info |
| `chmod 644 file` | Change permissions | rw-r--r-- |
| `chmod u+x file` | Add execute (owner) | Make executable |
| `chown user:group file` | Change owner | New owner |

## Link Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ln file link` | Create hard link | Same inode |
| `ln -s source link` | Create soft link | Shortcut |
| `ls -l link` | Identify link type | Shows rw- or l→ |
| `ln -i file link` | Show inode | Compare numbers |
| `unlink link` | Remove link | Delete without affecting original |

## Mount Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `mount` | Show mounts | All mounted filesystems |
| `mount /dev/sda1 /mnt` | Mount partition | Attach to directory |
| `umount /mnt` | Unmount | Detach filesystem |
| `mount -o remount,rw /` | Remount read-write | Change permissions |
| `cat /proc/mounts` | Current mounts | Kernel view |

## Filesystem Maintenance

| Command | Purpose | Example |
|---------|---------|---------|
| `fsck /dev/sda1` | Check filesystem | Fix errors (offline) |
| `fsck.ext4 /dev/sda1` | ext4 specific | Check ext4 only |
| `e2fsck -f /dev/sda1` | Force check | Bypass journal |
| `fstrim /mnt` | Trim unused blocks | SSD optimization |
| `tune2fs -l /dev/sda1` | Filesystem info | Details |

## Permission Reference

| Permission | Number | Meaning |
|------------|--------|---------|
| r (read) | 4 | View/list contents |
| w (write) | 2 | Modify/delete |
| x (execute) | 1 | Run/enter directory |
| rw- | 6 | Read + write |
| rwx | 7 | All permissions |
| r-- | 4 | Read only |

## Permission Format (rwxrwxrwx)

| Position | Meaning | Example |
|----------|---------|---------|
| 1st char | File type (- = file, d = dir, l = link) | `-` |
| 2-4 | Owner (user) | `rw-` |
| 5-7 | Group | `r--` |
| 8-10 | Others | `r--` |
| Octal | All three combined | 644 (rw-r--r--) |

## Common Permission Patterns

| Mode | Octal | Use Case |
|------|-------|----------|
| rwxr-xr-x | 755 | Executable, readable by all |
| rw-r--r-- | 644 | Regular file, owner can edit |
| rwx------ | 700 | Owner only (private) |
| rwxrwxrwx | 777 | Everyone can do everything |
| rw------- | 600 | Owner can read/write only |

## Inode Information

| Concept | Meaning | Notes |
|---------|---------|-------|
| Inode | Metadata container | Contains permissions, owner, timestamps |
| Inode number | Unique ID per file | Same for hard links |
| Hard link | Multiple names, same inode | Same permissions, size |
| Soft link | Shortcut to file | Different inode, follows original |
| Max hardlinks | Limited by filesystem | Typically 32000+ |

## Filesystem Types

| Filesystem | Pros | Cons |
|------------|------|------|
| ext4 | Standard, stable | Limited features |
| xfs | High performance | Less recovery tools |
| btrfs | Modern, snapshots | Experimental |
| FAT32 | USB compatible | Limited file size |
| NTFS | Windows native | Less Linux support |

## Troubleshooting

| Problem | Command | Solution |
|---------|---------|----------|
| Disk full | `du -sh /*` | Find and delete large dirs |
| No inodes | `df -i` | Delete small files |
| Slow read | `fstrim /` | Optimize SSD |
| Can't mount | `mount` | Check if already mounted |
| Permission denied | `ls -l` | Fix with chmod/chown |

## Quick Reference

```bash
# Disk overview
lsblk; df -h; df -i

# Find large files
du -sh /* | sort -rh

# Set typical permissions
chmod 644 file      # rw-r--r-- (regular file)
chmod 755 script    # rwxr-xr-x (executable)
chmod 700 private   # rwx------ (owner only)

# Change owner
chown user:group file

# Create links
ln source hardlink
ln -s source softlink
```
