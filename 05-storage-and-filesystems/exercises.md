# 05 - Storage and Filesystems Exercises

## Easy Exercises (1-5)

### Exercise 1: List All Partitions
**Objective**: See disk layout.

**Task**:
- Run: `lsblk`
- How many partitions? (sda, sdb, etc)
- Which is largest?
- Which has a mount point?

---

### Exercise 2: Check Disk Space
**Objective**: Monitor available storage.

**Task**:
- Run: `df -h`
- What's total disk size?
- Used percentage?
- Available free space?
- Which filesystem is root?

---

### Exercise 3: Find Large Directories
**Objective**: Identify space consumers.

**Task**:
- Run: `du -sh /home /var /tmp /usr` 2>/dev/null
- Which is largest?
- What percentage of root disk?
- Could removing it free space?

---

### Exercise 4: Check Inode Usage
**Objective**: Understand inode exhaustion.

**Task**:
- Run: `df -i`
- What's inode usage %?
- Can you still write files?
- What happens if 100% used?

---

### Exercise 5: View File Permissions
**Objective**: Read permission format.

**Task**:
- Run: `ls -l /etc/passwd`
- Output format: `-rw-r--r-- root root`
- What do: r, w, x mean?
- Who can read? (owner, group, other?)
- Who can write?

---

## Medium Exercises (6-10)

### Exercise 6: Create Hard and Soft Links
**Objective**: Understand link types.

**Task**:
- Create hard link: `ln /etc/hostname hw_link`
- Create soft link: `ln -s /etc/hostname sw_link`
- Check inodes: `ls -i hw_link sw_link /etc/hostname`
- Are hard link inodes same? (should be)
- Are soft link inodes same? (should be different)
- Edit original, check both links

---

### Exercise 7: Change File Ownership
**Objective**: Modify file permissions.

**Task**:
- Create file: `touch testfile`
- Check owner: `ls -l testfile`
- Change owner: `sudo chown $USER:$USER testfile` (if needed)
- Change permissions: `chmod 644 testfile`
- Check again: `ls -l testfile`
- What changed?

---

### Exercise 8: Find All Large Files
**Objective**: Locate space wasters.

**Task**:
- Find files >100MB: `find / -size +100M 2>/dev/null`
- Find in /home: `find /home -size +50M 2>/dev/null`
- Find in /var: `find /var -size +10M 2>/dev/null`
- Total disk usage: `du -sh /home /var /usr`

---

### Exercise 9: Monitor Disk Usage Over Time
**Objective**: Track space changes.

**Task**:
- Initial: `df -h /`
- Create large file: `dd if=/dev/zero of=testfile bs=1M count=100`
- Check disk: `df -h /`
- How much used changed?
- Delete file: `rm testfile`
- Check again: `df -h /`

---

### Exercise 10: Understand Mount Points
**Objective**: See how filesystems connect.

**Task**:
- List mounts: `mount | grep -E "^/dev"`
- Check /proc mounts: `cat /proc/mounts | head`
- View mount details: `df /home`
- What filesystem type? (ext4, xfs, etc)
- Mount point path?

---

## Submission Tips

1. Use `lsblk` for clear partition view
2. Use `du -sh` for directory totals
3. Remember: hard link = same inode, soft link = different
4. Permissions: r=4, w=2, x=1 (octal)
5. Monitor with: `df -h`, `du -sh`
