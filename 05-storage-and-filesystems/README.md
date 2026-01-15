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

Linux uses a hierarchical directory structure starting from the root directory (`/`). Every file and directory lives somewhere in this tree. Understanding the standard layout is essential for navigating and administering Unix-like systems.

**Root Directory (`/`)**
- The top-level directory containing everything
- All paths either start from `/` (absolute) or are relative to current directory
- Unlike Windows with drive letters (C:, D:), Linux has one unified tree
- No separate filesystem boundaries at the directory level - multiple filesystems can be mounted

**Essential System Directories**

**/bin - Essential Commands**
- Contains critical executables needed for system operation
- Available even when other filesystems are not mounted
- Examples: `ls`, `cat`, `grep`, `cp`, `grep`
- If you delete `/bin`, system may become unbootable

**/etc - Configuration Files**
- Stores system configuration files
- Text files that control system behavior
- Examples: `/etc/passwd` (user accounts), `/etc/fstab` (filesystem mounts), `/etc/nginx/nginx.conf` (web server config)
- Regular users can read most files, only root can modify
- Backing up `/etc` is critical for disaster recovery

**/home - User Home Directories**
- Each user gets `/home/username` directory
- User can create files and directories here
- User has full permissions in their home directory
- Not deleted if program crashes or system updates
- Size can grow large with personal files

**/tmp - Temporary Files**
- Applications write temporary files here
- Any user can create files - writable by all
- Files are typically deleted on system reboot (varies by distro)
- Should never store important data here
- World-writable but sticky bit prevents deleting others' files

**/var - Variable Data**
- Data that changes frequently during system operation
- Logs: `/var/log/` stores all system logs
- Cache: `/var/cache/` stores cached data
- Spool: `/var/spool/` for print queues, mail
- Can grow large - should monitor to prevent disk full
- Important for troubleshooting - all system events logged here

**/opt - Third-Party Applications**
- Optional software installed by admin
- Large applications that don't follow standard structure
- Example: `/opt/nodejs/`, `/opt/docker/`
- User-installed applications, not from OS package manager

**/usr - User Programs and Data**
- Second-largest directory after `/var`
- Programs: `/usr/bin`, `/usr/sbin`
- Libraries: `/usr/lib`, `/usr/lib64`
- Shared data: `/usr/share` (man pages, documentation)
- Source code: `/usr/src`
- Read-only on many systems

**/mnt - Mount Points**
- Traditional location for temporary mount points
- Where removable media (USB drives) are mounted
- Where network shares are mounted
- Empty directories where filesystems are attached

**Other Important Directories**
- **/root** - Home directory for root user (not `/root`, the directory)
- **/boot** - Kernel and bootloader files
- **/dev** - Device files (disks, terminals, random number generators)
- **/proc** - Virtual filesystem showing running processes
- **/sys** - Virtual filesystem showing hardware information
- **/lib** - Essential libraries needed by `/bin` and `/sbin`
- **/lost+found** - Where filesystem recovery stores orphaned files

### 2. Disk Organization

Storage is organized into multiple layers: physical disks, partitions, filesystems, and logical structures within filesystems.

**Disk Partitions**
- Physical disk (e.g., `/dev/sda`) is divided into separate partitions
- Each partition acts as independent storage region with separate filesystem
- Partition table at beginning of disk defines where each partition starts/ends
- Example: `/dev/sda1` (first partition), `/dev/sda2` (second partition)
- Partitions can be resized, deleted, created with tools like `fdisk`, `parted`
- Boot partition must be accessible early during system startup
- Root partition (`/`) contains core system files

**Mount Points**
- A mount point is a directory where a partition (or other storage) is attached
- Accessing that directory transparently accesses the partition's filesystem
- Example: `/dev/sda1` mounted at `/` means files in root directory come from sda1
- A single physical disk's multiple partitions are usually mounted at different directories
- Mount is temporary - survives reboot only if in `/etc/fstab`
- Without mounting a partition, its data is inaccessible

**Inodes (Index Nodes)**
- Core data structure representing a file or directory
- Stores all metadata: size, owner, permissions, timestamps, disk blocks
- Does NOT store the filename - that's stored in directory entry
- Each inode has unique number on its filesystem
- Limited inode space can cause "no space left on device" even with free disk space
- `ls -i` shows inode numbers
- Hard links create multiple directory entries pointing to same inode

**Blocks**
- Fixed-size chunks of disk space (typically 4096 bytes = 4KB)
- File data stored across multiple blocks
- Filesystem tracks which blocks belong to which inode
- Fragmentation: file spread across many non-contiguous blocks (slower)
- Block allocation strategy affects performance and space efficiency

**Filesystem Structure**
```
Disk Layout:
[Boot Sector] [Superblock] [Inode Table] [Data Blocks]
                    ↓
            [Filesystem Metadata]
            - Block size
            - Total inodes
            - Filesystem type
            - Journaling info
```

### 3. Filesystem Types

Different filesystems have different design goals, features, and performance characteristics. Choosing the right filesystem matters for workload.

**ext4 (Fourth Extended Filesystem)**
- Most common Linux filesystem
- Default on most distributions
- Mature and stable - production-proven
- Features: journaling (prevents corruption), extents (efficient storage), large file support
- Good general-purpose choice
- Practical limit: files up to 16TB, filesystem up to 1EB
- Performance: good for most workloads
- Use when: no special requirements, maximum compatibility needed

**XFS**
- High-performance filesystem designed by SGI
- Excellent for large files and high-concurrency workloads
- Features: extent-based, fast allocation, good for servers
- Default on RedHat/CentOS
- Practical limit: files up to 8EB, filesystem up to 8EB
- Performance: excellent for large I/O operations
- Limitation: slower than ext4 for very small files
- Use when: handling large files, high throughput needed (databases, video servers)

**Btrfs (B-tree filesystem)**
- Modern filesystem with advanced features
- Features: snapshots (point-in-time copies), copy-on-write, subvolumes, checksums
- Automatic data scrubbing detects and repairs corruption
- RAID support built-in
- Still considered less stable than ext4 in production
- Can shrink filesystem online (ext4 cannot)
- Practical limit: files up to 16EB, filesystem up to 16EB
- Use when: advanced features needed, willing to accept some risk

**FAT32**
- Simple filesystem, cross-platform
- Used on USB drives, memory cards, legacy systems
- Limitations: 4GB file size limit, no permissions, no journaling
- Fast but no reliability features
- Use when: compatibility with Windows/Mac needed, throwaway storage

**NTFS**
- Windows filesystem, can be used on Linux with drivers
- Supports large files, permissions
- Less efficient on Linux
- Use when: Windows interoperability required

**Choosing a Filesystem**
- **Desktop/General**: ext4 (safe default)
- **High performance servers**: XFS
- **Snapshots/advanced features**: Btrfs (experimental) or LVM + ext4
- **Portable drives**: FAT32 or exFAT
- **Compatibility**: Whatever system expects

### 4. Permissions and Access Control

File permissions control who can read, write, and execute files. Understanding permissions is critical for security.

**Permission Types**

**Read (r)**
- For files: ability to view file contents with `cat`, `less`, etc.
- For directories: ability to list contents with `ls`
- Without read on directory, cannot see what files are inside
- Does not grant ability to delete files (delete requires write on directory)

**Write (w)**
- For files: ability to modify file contents, append, truncate
- For directories: ability to create, delete, rename files inside
- Write permission on directory is what lets you delete files (not write permission on files)
- Without write, cannot modify even if you own the file

**Execute (x)**
- For files: ability to run file as program
- For directories: ability to enter directory with `cd` and access its contents
- Without execute on directory, cannot access anything inside, even if you have read
- Execute on directory is independent of read - can enter without listing contents

**Three Sets of Permissions**

**Owner (User)**
- The user who owns the file
- Shown first in permission string: `rwx-------`
- Owner can change permissions with `chmod`
- Owner has special privileges - can restrict own access if desired

**Group**
- A group of users (not individuals)
- Shown second in permission string: `---rwx----`
- All members of group have same permissions
- Useful for collaborative work - multiple users can access shared files
- Users can belong to multiple groups

**Others**
- Everyone else on the system
- Shown third in permission string: `------rwx`
- Least trust model - assume others are potential security risk
- Restrict what others can do to sensitive files

**Permission Notation**
- Octal: `755` = `rwxr-xr-x` = owner can do anything, group and others can read and execute
- Symbolic: `u+w` = add write for user, `g-x` = remove execute for group
- Default permissions affected by `umask`

**Permission Examples**
```
755 (rwxr-xr-x): Typical program - owner edits, others run
644 (rw-r--r--): Typical file - owner edits, others read
600 (rw-------): Private file - only owner can access
700 (rwx------): Private directory - only owner can enter
```

### 5. Hard Links vs Soft Links

Links allow files to be accessed through multiple paths. Understanding the difference is important for filesystem management.

**Hard Link**
- Additional directory entry pointing to the same inode number
- Multiple paths, single inode, single data
- Created with `ln file hardlink`
- Indistinguishable from original - both are equally valid
- Occupies no extra disk space (just directory entry, not file data)
- Cannot create hard link across filesystems (inode numbers are per-filesystem)
- Deleting one hard link doesn't affect others - file exists as long as any hard link exists
- Cannot create hard link to directory (would break filesystem tree assumptions)
- Modifying via one hard link affects all - they're truly the same file
- Use when: true aliases needed, don't want disk space overhead

**Soft Link (Symbolic Link)**
- Special file that contains path to another file
- Created with `ln -s target softlink`
- Can point across filesystems - just stores path string
- If target is deleted, soft link breaks (points to non-existent file)
- Consumes minimal disk space (just path string)
- Can link to directories
- Can create chains: link to link to link (though risky)
- `ls` shows with arrow: `softlink -> target`
- Breaking the link doesn't affect original - but following broken link fails
- Use when: need links across filesystems, don't care about target deletion, want it to be obvious it's a link

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

