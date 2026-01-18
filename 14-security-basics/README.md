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

**Authentication (AuthN)**: Verifies WHO you are. Methods: passwords, SSH keys, tokens, certs, MFA. Example: login succeeds if password correct.

**Authorization (AuthZ)**: Determines WHAT you can do. Methods: RBAC (roles have permissions), file permissions (rwx), API scopes. Example: admin role can delete, user role cannot.

**Key Difference**: Password correct (auth ✓) but no delete permission (authz ✗) = blocked. Both required. Access control models: DAC (owner controls, Unix chmod), MAC (system enforces, SELinux), RBAC (role-based), ABAC (attribute-based).

### 2. Encryption Types

**Symmetric (Same key encrypts/decrypts)**: Fast, good for large data. Examples: AES, DES. Use: disk encryption, VPN, data files. Challenge: key distribution.

**Asymmetric (Public key encrypts, private key decrypts)**: Slow, solves key distribution. Examples: RSA, ECDSA. Use: TLS handshake, SSH keys, digital signatures.

**Hashing (One-way, cannot reverse)**: Input → hash (irreversible). Examples: SHA256, bcrypt. Use: password storage, integrity verification. Add salt to prevent rainbow tables.

**TLS/SSL (Hybrid)**: Asymmetric for handshake (public/private exchange), symmetric for bulk data (fast). Best of both: security + performance.

### 3. SSH Key-Based Authentication

**Private Key (id_rsa)**: Secret credential (like password). Permissions: chmod 600. Never share. If compromised, attacker has access.

**Public Key (id_rsa.pub)**: Shareable. Stored in ~/.ssh/authorized_keys on target servers. Can be public.

**Authentication Flow**: Server stores public key. Client initiates SSH. Server sends challenge. Client signs with private key. Server verifies signature with public key. If valid → access granted. No password transmitted.

**Best Practices**: Generate 4096-bit RSA or ED25519. Add passphrase to private key. Backup securely. Rotate periodically. One key per client. Audit authorized_keys. Use SSH agent to cache passphrase (ssh-add, ssh-agent).

### 4. Certificates and Certificate Authority

**X.509 Certificates**: Standard format with public key, domain, issuer, expiration. Signed by CA. Proves "This key belongs to example.com".

**Certificate Authority (CA)**: Trusted third party verifies domain ownership and signs certificates. Examples: Let's Encrypt (free), DigiCert (paid). Root CAs built into browsers.

**Certificate Chain**: End-entity cert (example.com) → Intermediate cert(s) → Root cert. Browser verifies full chain back to root CA.

**Expiration**: Certs expire (usually 1 year). Must renew before expiration. Monitor expiration dates. Use auto-renewal (Let's Encrypt supports it).

**Self-Signed**: You sign your own. Only you trust it. Browsers show warning. Use only for testing/development, not production.

**Wildcard**: *.example.com covers all subdomains. One level only.

### 5. Security Layers and Defense in Depth

**Network Layer**: Firewall (inbound/outbound), VPN (encrypt between networks), network segmentation. Prevents external attacks.

**Transport Layer**: TLS/SSL (encrypt in transit), SSH (remote access), IPsec. Prevents eavesdropping.

**Application Layer**: User auth (password, API keys), authorization (RBAC), input validation (prevent injection), rate limiting (prevent brute force), logging.

**Data Layer**: Encryption at rest (disk), hashing (passwords), tokenization (replace sensitive data), masking (hide in logs).

**Defense in Depth Principle**: Multiple independent layers. Attacker must bypass all. If one compromised, others still protect. Example: password + firewall + SSL + disk encryption.

**Zero Trust**: Never trust, always verify. Encrypt everything (transit + rest). Least privilege (minimum permissions). Monitor everything.

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

