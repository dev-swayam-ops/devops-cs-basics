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

Linux filesystem is a unified tree starting from `/` (unlike Windows with drive letters).

**Essential Directories**:
- **/bin** - Critical commands (ls, cat, grep)
- **/etc** - System configs (passwd, fstab, nginx.conf) → **Back this up!**
- **/home** - User directories (personal files)
- **/tmp** - Temporary files (auto-cleaned on reboot)
- **/var** - Variable data (logs in `/var/log`, cache, mail)
- **/opt** - Third-party apps
- **/usr** - Programs & libraries
- **/mnt** - Mount points for storage
- **/dev** - Device files
- **/proc, /sys** - Virtual filesystems (processes, hardware info)

**DevOps Relevance**: Monitor `/var` (grows with logs), `/tmp` (can fill up), `/home` (user data). Know where config files live.

### 2. Disk Organization

**Partitions**: Physical disk divided into separate regions. Example: `/dev/sda1`, `/dev/sda2`. Each has own filesystem.

**Mount Points**: Directory where partition is "attached." Example: `/dev/sda1` mounted at `/` = root directory data comes from that partition.

**Inodes**: Data structure storing file metadata (size, owner, permissions, timestamps, location). Each file has one inode. Inode numbers are per-filesystem.

**Problem**: Running out of inodes ≠ running out of disk space. Can have free disk but no inodes left → "no space left on device" error.

**Check with**: `df -i` (inode usage), `df -h` (disk space), `lsblk` (partitions/mounts)

### 3. Filesystem Types

**ext4** - Default, stable, good for everything. Proven in production. **Use this unless you need something specific.**

**XFS** - High-performance for large files & servers. Default on RHEL/CentOS. Better for databases, video.

**Btrfs** - Modern, has snapshots & copy-on-write. Still experimental. Avoid unless you need advanced features.

**DevOps Relevance**: Understand what's running on your systems. XFS servers handle large I/O better. ext4 is safe default.

### 4. Permissions

File permissions: **read (r)**, **write (w)**, **execute (x)**.

**Three sets**: Owner (user), Group, Others.

**Notation**: `755` = `rwxr-xr-x` (owner: all, others: read+execute)

**Examples**:
- `755` - Program (owner edits, others run)
- `644` - File (owner edits, others read)
- `600` - Private (only owner)
- `700` - Private directory

**Important**: Write on directory = can delete files inside. Execute on directory = can enter it.

**Commands**: `chmod 755 file`, `chown user:group file`

### 5. Hard Links vs Soft Links

**Hard Link**: Multiple directory entries → same inode = same data. Use: `ln file hardlink`
- No disk overhead (just directory entry)
- Cannot cross filesystems
- Deleting one doesn't affect others (file exists as long as any link exists)

**Soft Link (Symlink)**: Directory entry → path to another file. Use: `ln -s target softlink`
- Can cross filesystems
- Takes minimal disk space (just path)
- If target deleted → broken link (shows as `link -> target` in red)
- Can link to directories

**DevOps**: Use soft links for flexibility (cross-filesystem). Use hard links rarely (only if you need hard guarantee file doesn't disappear).

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

