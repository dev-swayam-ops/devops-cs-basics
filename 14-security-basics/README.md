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

These are fundamentally different security concepts that work together to control access.

**Authentication (AuthN)**
- Verifies who you are
- Proves identity: "I am Alice"
- Question: "Who are you?"
- Methods: passwords, SSH keys, tokens, certificates, biometrics
- Example: logging into bank website with username and password
- Happens first: must authenticate before authorizing
- Failure: "Authentication failed" / "Invalid credentials"
- Can be single-factor (password) or multi-factor (password + code)

**Authorization (AuthZ)**
- Determines what you can do after authentication
- Grants permissions: "Alice can read file X, but not delete it"
- Question: "What are you allowed to do?"
- Happens after authentication
- Example: after logging in, restricted to only your account
- Failure: "Access denied" / "Permission denied"
- Role-based: admin role has different permissions than user role
- Resource-based: file has permissions (rwx) for owner, group, others

**Common Confusion**
- Authentication: WHO you are (verify identity)
- Authorization: WHAT you can do (allow actions)
- Both needed: authentic but no permission = blocked
- Example: Password correct (auth ✓) but user is guest (authz ✗) = blocked

**Access Control Models**
- DAC (Discretionary): Owner controls permissions (Unix files)
- MAC (Mandatory): System enforces policy (SELinux)
- RBAC (Role-based): Users have roles, roles have permissions (Active Directory)
- ABAC (Attribute-based): Fine-grained policies based on attributes (policies)

### 2. Encryption Types

Encryption transforms readable data into unreadable ciphertext. Different types serve different purposes.

**Symmetric Encryption**
- Same key encrypts and decrypts
- Example: password is "secret123", encrypt/decrypt with same key
- Fast: good for large data
- Examples: AES, DES, 3DES
- Key management challenge: how to share key securely?
- Use: encrypting files, disk encryption, VPN
- Security: key length determines strength (128-bit, 256-bit common)

**Asymmetric Encryption (Public-Key Cryptography)**
- Two keys: public and private
- Public key: encrypt (share with everyone)
- Private key: decrypt (keep secret)
- Slower: computationally expensive
- Examples: RSA, ECDSA
- Advantage: no need to share secret key
- Use: SSL/TLS, SSH keys, digital signatures
- Key pair: public and private mathematically related

**Hashing (One-Way)**
- Not encryption: cannot reverse
- Input → hash output (cannot go back)
- Example: password123 → a3f4b5c... (cannot reverse)
- Same input always produces same hash
- Used for: password storage, integrity checking, fingerprinting
- Examples: SHA256, SHA512, bcrypt, scrypt
- Security: should not be able to find input given hash
- Salt: random data added before hashing (prevents rainbow tables)

**Hybrid Approach (TLS/SSL)**
- Asymmetric: initial handshake, public/private keys
- Symmetric: bulk data transfer with session key
- Best of both: security (public key) + performance (symmetric)
- Process: exchange keys asymmetrically, then use symmetric for data

### 3. SSH Key-Based Authentication

SSH keys provide secure, password-less authentication. Understanding key management is critical for DevOps.

**Key Pair Components**

**Private Key (id_rsa)**
- Secret credential - like password
- Never share, never transmit
- Stored locally: ~/.ssh/id_rsa
- Permissions: 600 (-rw-------)
- Used by client to authenticate
- Protects account: if compromised, attacker can access

**Public Key (id_rsa.pub)**
- Shareable identifier
- Stored on servers you want to access
- In ~/.ssh/authorized_keys on target
- Can be shared freely
- Used by server to verify client
- Like a lock, private key is the lock-pick

**How It Works**
1. Server stores your public key in authorized_keys
2. Client initiates SSH connection
3. Server sends random challenge
4. Client signs challenge with private key
5. Server verifies signature with public key
6. If signature valid: authentication succeeds
7. Connection established without password

**Advantages Over Passwords**
- No password to intercept
- No brute force attacks (key too complex)
- Can revoke by removing public key
- Can restrict by key (which client)
- Can add passphrase for extra security
- Automatable: scripts can use keys without interaction

**Key Management Best Practices**
- Generate strong keys: 4096-bit RSA (or ED25519)
- Protect private key: chmod 600, passphrase
- Backup: keep secure backup
- Rotate: periodically generate new keys
- One key per client: compromise doesn't affect all
- Audit: regularly check authorized_keys

**SSH Agent**
- Holds decrypted private key in memory
- Unlocks with passphrase once
- Subsequent connections use agent (no re-entry)
- ssh-add: add key to agent
- ssh-agent: starts new agent session

### 4. Certificates and Certificate Authority

Certificates prove identity and enable encryption for websites and services.

**X.509 Certificates**
- Standard format for digital certificates
- Contains: public key, domain name, issuer, expiration date
- Signed by Certificate Authority (CA)
- Browser trusts CA, therefore trusts certificate
- Proves: "This key belongs to example.com"

**Certificate Components**
- Subject: who the certificate is for (example.com)
- Issuer: who signed it (Let's Encrypt, DigiCert, etc.)
- Public key: embedded in certificate
- Validity period: start and end dates
- Serial number: unique identifier
- Signature: CA's signature (proves CA approved)

**Certificate Authority (CA)**
- Trusted third party
- Verifies domain ownership
- Signs certificates
- Examples: Let's Encrypt (free), DigiCert, GlobalSign (paid)
- Root CAs: built into browsers/OS
- Intermediate CAs: organizations that issue certs

**Certificate Chain**
- End-entity cert (example.com): actual website cert
- Intermediate cert(s): bridge to root
- Root cert: ultimate authority (built-in to browsers)
- Verification: browser checks full chain back to root
- Chain needed because intermediate CA doesn't have direct trust

**Expiration and Renewal**
- Certificates expire (usually 1 year)
- Must be renewed before expiration
- Renewal: simple process with same domain
- Monitoring: should track expiration dates
- Auto-renewal: services like Let's Encrypt support it

**Self-Signed Certificates**
- You sign your own certificate
- No CA involved
- Only you trust it (not browsers)
- Browsers show warning: "Not trusted"
- Use only for: testing, internal systems, development
- Not suitable for: production websites, customer-facing

**Wildcard Certificates**
- Covers multiple subdomains
- Example: *.example.com covers api.example.com, www.example.com, etc.
- Cost less than multiple individual certs
- Limitation: only one level of subdomain

### 5. Security Layers and Defense in Depth

Security is strongest when implemented at multiple layers. No single mechanism provides complete protection.

**Network Layer**
- Firewall: controls inbound/outbound traffic
- VPN: encrypts traffic between networks
- Intrusion detection: monitors for attacks
- Network segmentation: separate trusted from untrusted

**Transport Layer**
- TLS/SSL: encrypts data in transit
- SSH: secure remote access with encryption
- IPsec: encryption at network layer
- Prevents eavesdropping on network

**Application Layer**
- User authentication: username/password, API keys
- Authorization: permissions, role-based access
- Input validation: prevents injection attacks
- Rate limiting: prevents brute force attacks
- Logging: tracks who did what

**Data Layer**
- Encryption at rest: encrypted on disk
- Hashing: one-way function for passwords
- Tokenization: replace sensitive data with tokens
- Data masking: hide sensitive data in logs/backups

**Defense in Depth Principle**
- No single point of failure
- Attacker must bypass multiple layers
- Each layer independent
- Example: password + firewall + SSL + file encryption
- If one layer compromised: others still protect

**Zero Trust Model**
- Never trust, always verify
- Assume breach: verify every request
- Encrypt everything: in transit and at rest
- Least privilege: minimum permissions needed
- Monitor everything: detect anomalies

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

