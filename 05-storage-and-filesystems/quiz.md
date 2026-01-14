# 05 - Storage and Filesystems: Quiz

## Multiple Choice (7 Questions)

### Q1: What does an inode contain?
A) File contents  
B) Metadata (ownership, permissions, size, timestamps)  
C) Directory entries  
D) Disk block addresses only

**Answer: B** - Inode stores metadata, not content

---

### Q2: Can hard links exist across filesystems?
A) Yes, always  
B) No, never  
C) Only for directories  
D) Only with special permissions

**Answer: B** - Inodes are unique per filesystem

---

### Q3: What happens when you delete a symlink target?
A) Symlink still works  
B) Symlink becomes broken  
C) System repairs it automatically  
D) Symlink converts to hard link

**Answer: B** - Symlink points to filename, broken if target gone

---

### Q4: Which RAID level provides both speed and redundancy?
A) RAID 0  
B) RAID 1  
C) RAID 5  
D) RAID 6

**Answer: C** - RAID 5 uses striping + parity

---

### Q5: What does "df" command show?
A) Disk usage by file  
B) Disk space by filesystem  
C) Disk errors  
D) Disk fragmentation

**Answer: B** - df = disk free (by mount point)

---

### Q6: Filesystem journaling prevents data loss by:
A) Creating backups  
B) Recording changes before writing to disk  
C) Compressing files  
D) Encrypting data

**Answer: B** - Journal allows recovery if crash occurs

---

### Q7: What command finds files larger than 1GB?
A) `ls -l`  
B) `du -sh`  
C) `find / -size +1G`  
D) `df -h`

**Answer: C** - find with -size predicate

---

## Short Answer (3 Questions)

### Q8: Explain hard links vs symbolic links

**Answer:**
- **Hard Link**: Points to same inode number, survives target deletion, can't span filesystems
- **Symlink**: Points to filename (like shortcut), breaks if target deleted, can span filesystems

---

### Q9: Your `/home` partition is 95% full. What would you do?

**Answer:**
```bash
1. Find space hogs:
   du -sh /home/* | sort -rh

2. Identify deletable files:
   find /home -type f -name "*.log" -mtime +30

3. Compress old data:
   tar czf archive.tar.gz /home/user/old_project

4. Cleanup package caches:
   apt clean  # or yum clean

5. As last resort:
   Add new disk and mount on /home
```

---

### Q10: Design a backup strategy for production servers

**Answer:**
```
3-2-1 Rule:
- 3 copies of data
- 2 different media types
- 1 offsite location

Implementation:
1. Daily incremental backups to local disk
2. Weekly full backups to external hard drive
3. Monthly backups to cloud storage (AWS S3)

Verification:
- Backup success logs checked daily
- Monthly restore test to verify integrity
- Tracking: backup size, time, success rate

Recovery RTO/RPO:
- RTO (Recovery Time Objective): 4 hours
- RPO (Recovery Point Objective): 1 day
```

---

## Answer Key

| Q | Answer |
|---|--------|
| 1 | B |
| 2 | B |
| 3 | B |
| 4 | C |
| 5 | B |
| 6 | B |
| 7 | C |
| 8 | See explanation |
| 9 | See investigation steps |
| 10 | See backup plan |

---
