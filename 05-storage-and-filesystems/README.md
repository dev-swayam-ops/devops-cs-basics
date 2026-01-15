# 05 - Storage and Filesystems

## What You'll Learn

- Filesystem hierarchy and structure
- Disk partitions and mounting
- Filesystem types (ext4, xfs, btrfs)
- Disk usage analysis
- File permissions and ownership
- Inodes and hard links
- Filesystem performance

## Prerequisites

- Completed **04-memory-management**
- Basic Linux command knowledge
- Understanding of directories and files

## Key Concepts

### 1. Filesystem Hierarchy
```
/          = root
├── /bin   = essential programs (ls, cat, etc)
├── /etc   = configuration files
├── /home  = user home directories
├── /tmp   = temporary files
├── /var   = variable data (logs, caches)
├── /opt   = third-party applications
├── /usr   = user programs and libraries
└── /mnt   = mount points
```

### 2. Disk Organization
- **Partition**: Division of disk (e.g., /dev/sda1)
- **Mount Point**: Directory where partition is attached
- **Inode**: File metadata (not filename)
- **Block**: Unit of disk storage (4KB typical)

### 3. Filesystem Types
- **ext4**: Most common Linux
- **xfs**: High performance
- **btrfs**: Modern features (snapshots)
- **FAT32**: USB drives
- **NTFS**: Windows

### 4. Permissions (rwx)
- **r** (read): View file contents
- **w** (write): Modify file
- **x** (execute): Run as program (directory: enter)
- **3 sets**: owner, group, others

### 5. Hard Link vs Soft Link
- **Hard link**: Multiple names, same inode
- **Soft link**: Shortcut, different inode

## Hands-on Lab: Filesystem Exploration

### Lab Overview
Analyze partitions, mount points, and disk usage.

### Lab Commands

```bash
# 1. List partitions and mount points
lsblk

# Expected:
# NAME FSTYPE MOUNTPOINT
# sda
# ├─sda1 ext4 /
# └─sda2 ext4 /home

# 2. Show disk usage
df -h

# Expected:
# Filesystem Size Used Avail Use% Mounted on
# /dev/sda1   50G  20G  30G  40% /

# 3. Check inode usage
df -i

# Expected:
# Filesystem Inodes  Used Avail Use%
# /dev/sda1   1.3M  500K 800K  38%

# 4. Directory size
du -sh /home

# Expected: 2.5G /home

# 5. Largest directories
du -sh /* | sort -rh | head -5

# Expected:
# 3.2G /var
# 2.5G /home
# 1.8G /usr

# 6. View permissions
ls -l /home

# Expected:
# drwxr-xr-x user group 4096 Jan 15 home

# 7. Show inode number
ls -i /etc/passwd

# Expected: 654321 /etc/passwd

# 8. Create hard link
ln /etc/passwd passwd_link
diff /etc/passwd passwd_link

# Expected: (no diff, same file)

# 9. Create soft link
ln -s /etc/passwd passwd_shortcut
cat passwd_shortcut

# Expected: (passwd file contents)

# 10. Check filesystem type
df -T /

# Expected:
# Filesystem Type
# /dev/sda1 ext4
```

## Validation

```bash
# Can you see filesystems?
lsblk && echo "✓ Filesystems visible"

# Understand disk usage?
df -h && echo "✓ Disk usage shown"

# Know permissions?
ls -l && echo "✓ Permissions shown"

# Understand links?
echo "✓ Hard/soft links understood"
```

## Cleanup

```bash
# Remove test files
rm -f passwd_link passwd_shortcut
```

## Common Mistakes

1. **Confusing disk space and inodes**: Inodes separate from blocks
2. **Mounting wrong partition**: Check `df` before mount
3. **Filling root partition**: Monitor /var and /tmp
4. **Hard links across filesystems**: Not possible
5. **Permission bits order**: User, group, other (ugo)

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Disk full | Check: du -sh /*, find large files |
| No inodes | Delete small files, check: df -i |
| Slow filesystem | Use: fstrim, check fragmentation |
| Permission denied | Fix: chmod, chown |
| Broken soft link | Check: ls -l (shows red), fix path |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for commands
4. Move to **06-networking-fundamentals** after completion

## Additional Resources

- Filesystems: `man 5 fstab`, `man mount`
- Permissions: `man chmod`, `man chown`
- Disk tools: `man du`, `man df`, `man lsblk`

