# 05 - Storage and Filesystems Solutions

## Easy Solutions (1-5)

### Solution 1: List All Partitions

**Command**:
```bash
lsblk
```

**Output Example**:
```
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0  100G  0 disk
├─sda1   8:1    0   50G  0 part /
└─sda2   8:2    0   50G  0 part /home
sdb      8:16   0  200G  0 disk
```

**Answers**:
- Partitions: 2 (sda1, sda2)
- Largest: sdb (200G)
- Mount points: sda1 (/), sda2 (/home)

---

### Solution 2: Check Disk Space

**Command**:
```bash
df -h
```

**Output Example**:
```
Filesystem     Size Used Avail Use% Mounted on
/dev/sda1       50G  20G  30G  40% /
/dev/sda2       50G  25G  25G  50% /home
tmpfs          7.8G    0 7.8G   0% /dev/shm
```

**Answers**:
- Total: 100G
- Used: 45G (40% of sda1 root)
- Available: 55G
- Root filesystem: /dev/sda1

---

### Solution 3: Find Large Directories

**Command**:
```bash
du -sh /home /var /tmp /usr 2>/dev/null
```

**Output Example**:
```
25G  /home
8.5G /var
512M /tmp
12G  /usr
```

**Analysis**:
- Largest: /home (25G)
- If total disk 100G: 25% of space
- Removing /home would free 25G

---

### Solution 4: Check Inode Usage

**Command**:
```bash
df -i
```

**Output Example**:
```
Filesystem      Inodes Used Avail Use%
/dev/sda1       6.5M  2.1M 4.4M  32%
/dev/sda2       6.5M  4.2M 2.3M  64%
```

**Answers**:
- Current usage: 32-64%
- If 100% used: Cannot create files (even if space available)
- Still can write: Yes (32% is safe)

---

### Solution 5: View File Permissions

**Command**:
```bash
ls -l /etc/passwd
```

**Output Example**:
```
-rw-r--r-- 1 root root 1234 Jan 15 /etc/passwd
```

**Analysis**:
- Format: `-rw-r--r--`
  - `-` = file (not directory)
  - `rw-` = owner (read, write, NO execute)
  - `r--` = group (read only)
  - `r--` = others (read only)
- Who can read: owner, group, others (all)
- Who can write: owner only

---

## Medium Solutions (6-10)

### Solution 6: Create Hard and Soft Links

**Commands**:
```bash
# Create links
ln /etc/hostname hw_link
ln -s /etc/hostname sw_link

# Check inodes
ls -i hw_link sw_link /etc/hostname

# Edit original
echo "newname" | sudo tee /etc/hostname
cat hw_link
cat sw_link
```

**Output Example**:
```
654321 hw_link
654321 /etc/hostname    # Same inode (hard link)
654322 sw_link          # Different inode (soft link)

newname   # Hard link shows change
newname   # Soft link follows original
```

**Explanation**:
- Hard link: same inode, changes visible
- Soft link: different inode, redirects to original
- Soft link shows as: `lrwxrwxrwx`

---

### Solution 7: Change File Ownership

**Commands**:
```bash
# Create file
touch testfile
ls -l testfile

# Change owner (if needed)
sudo chown $USER:$USER testfile

# Change permissions
chmod 644 testfile
ls -l testfile
```

**Output Example**:
```
Before: -rw-r--r-- root root testfile
After:  -rw-r--r-- user user testfile
```

**Changes**:
- Owner: root → user
- Permissions: 644 = rw-r--r--
  - Owner: read + write
  - Group + others: read only

---

### Solution 8: Find All Large Files

**Commands**:
```bash
# Find >100MB
find / -size +100M 2>/dev/null | head

# Find in /home
find /home -size +50M 2>/dev/null

# Total usage
du -sh /home /var /usr
```

**Output Example**:
```
/var/cache/package.iso
/home/user/videos/movie.mp4
/usr/lib/jvm/java-11/

25G  /home
8.5G /var
12G  /usr
```

---

### Solution 9: Monitor Disk Usage

**Commands**:
```bash
# Before
df -h / | tail -1

# Create 100MB file
dd if=/dev/zero of=testfile bs=1M count=100

# After
df -h / | tail -1

# Delete
rm testfile

# Check again
df -h / | tail -1
```

**Output Example**:
```
Before: 30G available
After:  29.9G available  (100MB less)
After deletion: 30G available
```

---

### Solution 10: Understand Mount Points

**Commands**:
```bash
# View mounts
mount | grep -E "^/dev"

# Check /proc/mounts
cat /proc/mounts | head -5

# Details for /home
df -T /home
```

**Output Example**:
```
/dev/sda1 on / type ext4
/dev/sda2 on /home type ext4

/dev/sda2 on /home type ext4 (rw,relatime)

Filesystem Type Mounted on
/dev/sda2  ext4 /home
```

---

## Practice Tips

1. Use `lsblk` for quick view
2. `du -sh` for total size (not recursive)
3. `du -sh /*` for top-level breakdown
4. `find -size` for large file hunt
5. Links: `ls -i` shows inode
