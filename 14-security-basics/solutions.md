# 14 - Security Basics Solutions

## Easy Solutions (1-5)

### Solution 1: Generate SSH Key Pair

**Command**:
```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/test_key -N ""
cat ~/.ssh/test_key      # Private
cat ~/.ssh/test_key.pub  # Public
```

**Files Created**:
- id_rsa: Private key (keep secret)
- id_rsa.pub: Public key (share)
- Both needed for auth

---

### Solution 2: Check SSH Key Permissions

**Commands**:
```bash
ls -la ~/.ssh/test_key*
# Output:
# -rw------- (600) test_key       # Private: owner only
# -rw-r--r-- (644) test_key.pub   # Public: readable
```

**Why Different**:
- Private: sensitive, owner only
- Public: shareable, readable by all
- 644 on private = vulnerable to reading

---

### Solution 3: File Permissions Format

**Breakdown**:
```
-rw-r--r--
- = file (d=directory)
rw- = owner (read, write, no execute)
r-- = group (read only)
r-- = others (read only)

Octal: 644 (rw-r--r--)
```

**Common Modes**:
- 644: regular file (rw-r--r--)
- 755: executable (rwxr-xr-x)
- 600: private (rw-------)
- 700: private dir (rwx------)

---

### Solution 4: Authentication vs Authorization

**Definitions**:
- Auth: Proof of identity (password, key)
- AuthZ: What you're allowed to do (file access)

**Example**:
```
SSH login: auth (password or key)
File access: authz (owner/group/other)
Both needed for access
```

---

### Solution 5: Hash Passwords

**Command**:
```bash
echo -n "password" | sha256sum
# e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855

# Same password: same hash
# Different password: different hash
# Can't reverse: one-way function
```

**Better**: Bcrypt (slower, salt)
```bash
echo "password" | htpasswd -B -i - user
```

---

## Medium Solutions (6-10)

### Solution 6: Create User and Permissions

**Script**:
```bash
sudo useradd testuser
sudo groupadd testgroup
sudo usermod -aG testgroup testuser
id testuser
# Output: uid=1001, gid=1001 groups=1001(testgroup)

# File access
sudo chown testuser:testgroup testfile
chmod 644 testfile
ls -l testfile
# -rw-r--r-- testuser testgroup
```

---

### Solution 7: SSH Key-Based Auth

**Setup**:
```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/key
ssh-copy-id -i ~/.ssh/key user@host
ssh -i ~/.ssh/key user@host
# Connects without password
```

**Advantages**:
- No password transmission
- Stronger authentication
- Automation friendly
- Can disable passwords

---

### Solution 8: Certificate and SSL

**View Certificate**:
```bash
openssl x509 -in cert.pem -text -noout
# Shows:
# Subject: CN = example.com
# Issuer: C=US, O=Let's Encrypt
# Not Before: Jan 1, 2026
# Not After: Apr 1, 2026
```

**Expiry**:
- Before expiry: valid
- After expiry: browsers warn/reject

---

### Solution 9: Firewall Rules

**Commands**:
```bash
sudo ufw enable
sudo ufw allow 22/tcp   # SSH
sudo ufw allow 80/tcp   # HTTP
sudo ufw allow 443/tcp  # HTTPS
sudo ufw deny 23/tcp    # Telnet
sudo ufw status
```

**Best practice**: Default deny, whitelist needed

---

### Solution 10: Vulnerability Scanning

**Checks**:
```bash
# Users
grep "^[^#]" /etc/passwd | cut -d: -f1

# Open ports
ss -tuln | grep LISTEN

# Weak packages
apt list --upgradable
sudo apt update && sudo apt upgrade

# SSH config
grep "PermitRootLogin\|PasswordAuthentication" /etc/ssh/sshd_config
```

---

## Quick Security Checklist

```
SSH Keys: chmod 600 private, shared public
Permissions: chmod 644 files, 755 dirs
Users: strong passwords, least privilege
Firewall: default deny, allow needed
Updates: patch regularly
Certificates: renew before expiry
Encryption: TLS in transit, encryption at rest
```
