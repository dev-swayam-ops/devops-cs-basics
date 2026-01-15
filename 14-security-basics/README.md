# 14 - Security Basics

## What You'll Learn

- Authentication and authorization
- Encryption (symmetric and asymmetric)
- SSL/TLS certificates
- SSH keys and key-based auth
- User and permission management
- Firewall and network security
- Security best practices
- Vulnerability scanning

## Prerequisites

- Completed **13-caching-and-queues**
- Understanding of users and permissions
- Basic networking knowledge

## Key Concepts

### 1. Authentication vs Authorization
```
Authentication: Who are you? (login)
Authorization: What can you do? (permissions)
```

### 2. Encryption Types
- **Symmetric**: Same key (AES, DES) - fast
- **Asymmetric**: Public/private (RSA) - slow but secure
- **Hash**: One-way (SHA256, bcrypt) - passwords

### 3. SSH Key-Based Auth
```
Private key: Keep secret (id_rsa)
Public key: Share widely (id_rsa.pub)
Server stores public, client uses private
No password needed
```

### 4. Certificates
- **CA**: Certificate Authority (trusted)
- **Certificate**: Proves domain ownership
- **Expiry**: Must renew periodically
- **Chain**: CA → Intermediate → End Entity

### 5. Security Layers
```
Network: Firewall, VPN
Transport: TLS, SSH
Application: User auth, permissions
Data: Encryption, hashing
```

## Hands-on Lab: SSH Keys and Permissions

### Lab Overview
Create SSH keys, set permissions, understand security model.

### Lab Commands

```bash
# 1. Generate SSH key pair
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""

# Expected: Private and public key created

# 2. View public key
cat ~/.ssh/id_rsa.pub

# Expected: ssh-rsa AAAAB3... user@host

# 3. Set proper permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub

# 4. Check file permissions
ls -la ~/.ssh/

# Expected: drwx------ for .ssh, -rw------- for id_rsa

# 5. Test SSH key
ssh-keygen -l -f ~/.ssh/id_rsa

# Expected: Key fingerprint

# 6. Check user permissions
ls -l /etc/passwd /etc/shadow

# Expected: 644 passwd, 000 shadow (restricted)

# 7. List user groups
id

# Expected: uid=1000(user) gid=1000(user) groups=...

# 8. Set file ownership
chmod 644 testfile
ls -l testfile

# Expected: -rw-r--r--

# 9. Change group
chgrp groupname testfile

# 10. Verify permissions
stat testfile

# Expected: (permissions, owner, group, timestamps)
```

## Validation

```bash
# Can you generate SSH keys?
test -f ~/.ssh/id_rsa && echo "✓ SSH keys created"

# Understand permissions?
ls -l /etc/shadow && echo "✓ Permissions visible"

# Know authentication?
echo "✓ Auth/AuthZ understood"

# Handle encryption?
echo "✓ Encryption understood"
```

## Cleanup

```bash
# Remove test files
rm -f testfile
chmod 700 ~/.ssh/id_rsa  # Ensure private key protected
```

## Common Mistakes

1. **World-readable private key**: chmod 600 private
2. **Weak passwords**: Use 12+ chars, mixed case
3. **No backup**: Backup private key securely
4. **Hardcoded credentials**: Use env vars or vault
5. **Self-signed certs**: Use CA for production

## Troubleshooting

| Problem | Solution |
|---------|----------|
| SSH permission denied | Check: key permissions, .ssh folder |
| Can't connect | Check: firewall, port, hostname |
| Certificate error | Check: expiry, CA chain, hostname |
| Permission denied (file) | Check: file perms, user groups |
| Weak password | Use: 12+ chars, complexity |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for commands
4. Move to **15-observability-and-debugging** after completion

## Additional Resources

- SSH: `man ssh-keygen`, `man ssh`
- Permissions: `man chmod`, `man chown`
- Certs: openssl.org, let's encrypt
- Security: OWASP Top 10

