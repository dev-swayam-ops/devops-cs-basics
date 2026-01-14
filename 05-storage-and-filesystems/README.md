# 05 - Storage and Filesystems

## What You'll Learn

Master storage and filesystem concepts:
- Filesystem hierarchy and inodes
- Hard links and soft (symbolic) links
- Common filesystems (ext4, XFS, Btrfs, ZFS, NFS)
- Filesystem permissions and attributes
- RAID levels and redundancy
- Backup strategies
- Storage performance

## Prerequisites

- Completed **04-memory-management**
- Understanding of files and directories
- Basic Linux commands

## Key Concepts

### 1. Inode Structure
- **Inode**: Contains file metadata (not filename!)
- **Hard Link**: Multiple names pointing to same inode
- **Soft Link**: Pointer to filename (can cross filesystems)
- **File Descriptor**: Runtime handle to file

### 2. Filesystem Types
- **ext4**: Default Linux filesystem, journaling
- **XFS**: High-performance, scalable
- **Btrfs**: Copy-on-write, snapshots, RAID built-in
- **ZFS**: Enterprise, checksums, compression
- **NFS**: Network filesystem, remote mount

### 3. RAID Levels
- **RAID 0**: Striping (fast, no redundancy)
- **RAID 1**: Mirroring (redundancy, 50% capacity)
- **RAID 5**: Striping + parity (balance of speed/redundancy)
- **RAID 6**: Dual parity (protect against 2 disk failures)
- **RAID 10**: Stripe of mirrors (fast + redundancy)

### 4. Backup Strategy (3-2-1 Rule)
- 3 copies of data
- 2 different media types
- 1 copy off-site

## Hands-on Lab: Filesystem Operations

### Lab Steps

```bash
# 1. Check filesystem usage
df -h
du -sh /home/*

# 2. View filesystem details
mount | grep -E "^/dev"
cat /etc/fstab

# 3. Create hard link
touch original.txt
ln original.txt hardlink.txt
ls -i original.txt hardlink.txt  # Same inode

# 4. Create soft link
ln -s original.txt softlink.txt
ls -l softlink.txt  # Shows arrow

# 5. Check inode usage
df -i

# 6. Find files by inode
find / -inum 12345

# 7. View filesystem type
stat /

# 8. Check disk I/O
iostat -x 1

# 9. Monitor disk usage
iotop

# 10. Analyze directory size
ncdu /home
```

## Validation

Verify your filesystem knowledge:

```bash
# Can you understand inodes?
ls -i /etc/hostname && echo "✓ Inode viewing works"

# Can you create links?
touch test.txt && ln test.txt hardlink.txt && echo "✓ Hard links work"

# Can you check filesystem?
mount | head && echo "✓ Filesystem info visible"

# Can you understand RAID?
echo "✓ RAID concepts understood"
```

## Cleanup

```bash
rm -f original.txt hardlink.txt softlink.txt test.txt
```

## Common Mistakes

1. **Hard link across filesystems**: Can't hard link across filesystems (use soft link)
2. **Deleting soft link**: Deletes link, not target
3. **Running out of inodes**: Can run out even with free space
4. **RAID 0 no redundancy**: Single disk failure = total data loss
5. **Forgetting off-site backups**: 3-2-1 rule requires off-site copy

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Disk full | Check: du -sh, remove old files, compress |
| No free inodes | Check: ls -i, rm unnecessary files |
| Slow disk | Use: iostat, iotop, check RAID status |
| Corrupted filesystem | fsck (unmount first!) or repair |
| Soft link broken | Check: link target exists |

## Next Steps

1. Move to **06-networking-fundamentals** for network storage
2. Learn about NFS and block storage
3. Study backup and recovery tools (rsync, tar, backup software)
4. Explore LVM (Logical Volume Management)
5. Learn about snapshots and versioning

## Additional Resources

- Inode structure: `man inode`
- Filesystem types: `man filesystems`
- Disk usage: `man du`, `man df`
- RAID: https://en.wikipedia.org/wiki/RAID

