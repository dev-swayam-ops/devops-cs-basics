# 05 - Storage and Filesystems: Exercises

## Exercise 1: Explore Filesystem Hierarchy (Easy)

```bash
# View filesystem structure
df -h          # Show mounted filesystems
mount          # Show mount points
lsblk          # Show block devices

# Explore directories
ls -la /       # Root
ls -la /etc    # Configuration
ls -la /var    # Variable data
ls -la /dev    # Devices
ls -la /proc   # Processes
```

1. List 5 different filesystems on your system
2. What is the root filesystem mount point?
3. What filesystems are mounted as read-only?

---

## Exercise 2: Understand Inodes (Easy)

```bash
ls -i /etc    # Show inode numbers
stat /etc/passwd
```

1. What is an inode?
2. What information does an inode store?
3. Can two different files have same inode?

---

## Exercise 3: Create and Manage Hard Links (Medium)

```bash
echo "original" > file1.txt
ln file1.txt file1_hardlink.txt
ls -i file1*
cat file1_hardlink.txt
```

1. What is a hard link?
2. Do both files share the same inode?
3. If you delete file1.txt, can you still access content through hard link?

---

## Exercise 4: Symbolic Links (Medium)

```bash
ln -s file1.txt file1_symlink.txt
ls -la file1_symlink.txt
cat file1_symlink.txt
rm file1.txt
cat file1_symlink.txt  # What happens?
```

1. What is a symbolic link?
2. How is it different from hard link?
3. What happens when target is deleted?

---

## Exercise 5: Understand File Permissions on Filesystems (Medium)

```bash
touch testfile.txt
chmod 600 testfile.txt      # rw-------
ls -l testfile.txt
cat testfile.txt            # Can read
echo "test" >> testfile.txt # Can write
chmod 400 testfile.txt      # r--------
echo "test" >> testfile.txt # What happens?
```

1. How do filesystem permissions work?
2. What does "600" mean in octal?
3. What prevents write access?

---

## Exercise 6: Disk Usage Analysis (Medium)

```bash
du -sh ~                  # Total home size
du -sh ~/*                # Each subdirectory
df -h /home               # Filesystem usage
```

1. Which directories consume most space?
2. Calculate how much space remains
3. Identify what can be cleaned up

---

## Exercise 7: Filesystem Performance (Medium)

```bash
# Create test files
dd if=/dev/zero of=testfile bs=1M count=100

# Measure read speed
time dd if=testfile of=/dev/null bs=1M

# Measure write speed
time dd if=/dev/zero of=testfile2 bs=1M count=100
```

1. What is the read speed of your filesystem?
2. What is the write speed?
3. How does this affect database performance?

---

## Exercise 8: Filesystem Journaling (Medium)

```bash
# Check filesystem type and features
mkfs -t ext4 -F /tmp/testfs.img 1G
mount /tmp/testfs.img /mnt/test
tune2fs -l /dev/loop0 | grep -i journal
```

1. What is journaling?
2. Why is it important?
3. What filesystems support journaling?

---

## Exercise 9: Backup and Restore (Medium)

```bash
# Create archive
tar czf backup.tar.gz ~/important_dir

# List contents without extracting
tar tzf backup.tar.gz

# Extract to new location
mkdir restore_dir
tar xzf backup.tar.gz -C restore_dir
```

1. What is the difference between tar, gzip, and tar.gz?
2. How would you back up a live filesystem?
3. How do you verify backup integrity?

---

## Exercise 10: Filesystem Quotas and Limits (Medium)

```bash
# Check if quotas are enabled
mount | grep usrquota

# Set user quota
setquota -u username 1000000 1100000 0 0 /home

# Check quota
quota -u username

# View report
repquota /home
```

1. What are filesystem quotas?
2. How do quotas prevent disk exhaustion?
3. What's difference between soft and hard limits?

---
