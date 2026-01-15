# 14 - Security Basics Exercises

## Easy Exercises (1-5)

### Exercise 1: Generate SSH Key Pair
**Objective**: Create authentication credentials.

**Task**:
```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/test_key
```
- What files created? (private and public)
- What's difference?
- Private key: keep secret?
- Public key: share freely?

---

### Exercise 2: Check SSH Key Permissions
**Objective**: Secure the keys.

**Task**:
```bash
ls -la ~/.ssh/test_key*
```
- Private key perms? (should be 600)
- Public key perms? (644 OK)
- Why different?
- What if 644 on private? (vulnerable!)

---

### Exercise 3: Understand File Permissions
**Objective**: Know chmod model.

**Task**:
```bash
touch testfile
chmod 644 testfile
ls -l testfile
```
- Format: -rw-r--r--
- Owner: rw- (6)
- Group: r-- (4)
- Others: r-- (4)
- What's 755? (rwxr-xr-x)

---

### Exercise 4: Authentication vs Authorization
**Objective**: Know the difference.

**Task**:
- Authentication: proving identity (password, key)
- Authorization: what you can do (permissions)
- Example: Login is auth, file access is authz
- Both needed?

---

### Exercise 5: Hash Passwords
**Objective**: Understand one-way encryption.

**Task**:
```bash
echo -n "password123" | sha256sum
```
- Same input: same hash?
- Change input: different hash?
- Can you reverse? (no!)
- Use: password storage
- Bcrypt vs SHA256? (bcrypt slower, better)

---

## Medium Exercises (6-10)

### Exercise 6: Create User and Set Permissions
**Objective**: Access control.

**Task**:
```bash
sudo useradd testuser
sudo usermod -aG groupname testuser
id testuser
```
- Can user access file?
- Check: file owner, group, perms
- Change group: `chgrp`
- Add to group: `usermod -aG`

---

### Exercise 7: Test SSH Key-Based Auth
**Objective**: Keyless authentication.

**Task**:
```bash
ssh-copy-id -i ~/.ssh/test_key user@host
ssh -i ~/.ssh/test_key user@host
```
- Does password needed?
- Key auth: advantages?
- Can disable password? (yes, `PasswordAuthentication no`)

---

### Exercise 8: Certificate and SSL
**Objective**: HTTPS basics.

**Task**:
```bash
openssl x509 -in cert.pem -text -noout
```
- What's certificate?
- What's issuer? (CA)
- What's expiry?
- Expired cert: what happens? (error)

---

### Exercise 9: Firewall Rules
**Objective**: Network security.

**Task**:
```bash
sudo ufw status
sudo ufw allow 22/tcp    # Allow SSH
sudo ufw deny 23/tcp     # Block telnet
```
- Port 22: SSH (allow)
- Port 80: HTTP (allow)
- Port 443: HTTPS (allow)
- Others: deny by default?

---

### Exercise 10: Vulnerability Scanning
**Objective**: Find weak spots.

**Task**:
```bash
# Check weak passwords
grep -v "^#" /etc/shadow | cut -d: -f1
# Check open ports
netstat -tuln | grep LISTEN
# Check outdated software
apt list --upgradable
```
- Unused ports: close them
- Weak passwords: strengthen
- Outdated packages: patch

---

## Submission Tips

1. Always protect private keys (chmod 600)
2. Use strong passwords (12+ chars)
3. Keys over passwords when possible
4. Least privilege principle (minimal perms)
5. Regular security updates
