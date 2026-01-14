# 14 - Security Basics

## What You'll Learn

Master security fundamentals:
- Authentication vs authorization
- Encryption (symmetric and asymmetric)
- SSH keys and password management
- Firewalls and network security
- HTTPS/TLS and certificates
- Common vulnerabilities
- Security best practices
- Audit logging

## Prerequisites

- Completed **13-caching-and-queues**
- Understanding of networking and systems
- Security mindset

## Key Concepts

### 1. Authentication vs Authorization
- **Authentication**: Verify who you are (username/password, SSH keys)
- **Authorization**: What are you allowed to do (permissions, roles)
- Both are necessary: Good auth + bad authz = security hole

### 2. Encryption Types
- **Symmetric**: Same key to encrypt and decrypt (AES, DES) - fast
- **Asymmetric**: Public key encrypts, private key decrypts (RSA, ECDSA) - slower
- **Hashing**: One-way conversion (SHA256, bcrypt) - password storage

### 3. SSH Key Management
- Private key: Keep secret, never share
- Public key: Share widely, added to authorized_keys
- Passphrase: Optional password protecting private key

### 4. HTTPS/TLS
- SSL/TLS encrypts connection
- Certificate: Proves server identity
- CA (Certificate Authority): Issues certificates
- OCSP Stapling: Check revocation status

## Hands-on Lab: Security Practices

### Lab Steps

```bash
# 1. Generate SSH key pair
ssh-keygen -t ed25519 -f /tmp/testkey -N "passphrase"
ls -la /tmp/testkey*

# 2. Check key permissions
stat /tmp/testkey
chmod 600 /tmp/testkey

# 3. View public key
cat /tmp/testkey.pub

# 4. Copy SSH key (simulation)
cat /tmp/testkey.pub >> ~/.ssh/authorized_keys

# 5. Hash a password with bcrypt
echo -n "mypassword" | sha256sum
htpasswd -c /tmp/users admin

# 6. Check file permissions
ls -la /tmp/users
cat /tmp/users

# 7. View SSL certificate
openssl x509 -in /etc/ssl/certs/ca-certificates.crt -text -noout | head -20

# 8. Generate self-signed certificate
openssl req -x509 -newkey rsa:4096 -keyout /tmp/key.pem -out /tmp/cert.pem -days 365 -nodes

# 9. Check certificate validity
openssl x509 -in /tmp/cert.pem -text -noout | grep -A 2 Validity

# 10. Test HTTPS connection
curl -k https://localhost 2>/dev/null || echo "No local HTTPS server"
```

## Validation

Verify your security knowledge:

```bash
# Can you generate SSH keys?
ssh-keygen -t ed25519 -f /tmp/test -N "" && echo "✓ SSH key generation works"

# Can you check permissions?
ls -la /tmp/test && echo "✓ Permission checking works"

# Can you understand encryption?
echo "✓ Encryption concepts understood"

# Can you work with certificates?
echo "✓ Certificate concepts understood"
```

## Cleanup

```bash
rm -f /tmp/testkey* /tmp/users /tmp/key.pem /tmp/cert.pem /tmp/test /tmp/test.pub
```

## Common Mistakes

1. **Weak passwords**: Use password managers
2. **Shared SSH keys**: Each person gets their own key
3. **Unencrypted communication**: Always use TLS/HTTPS
4. **No logging**: Can't audit without logs
5. **Ignoring security updates**: Patch regularly!
6. **Overprivileged users**: Principle of least privilege
7. **Hardcoded secrets**: Use secret management tools

## Troubleshooting

| Problem | Solution |
|---------|----------|
| SSH key permission denied | Check: permissions 600, correct user |
| Certificate expired | Renew: certificate, check: system time |
| HTTPS connection refused | Verify: port listening, certificate valid |
| Weak cipher suite | Update: OpenSSL, enable modern TLS |
| Audit trail missing | Enable: logging, check: log location |

## Next Steps

1. Move to **15-observability-and-debugging** for monitoring security
2. Learn about secrets management (Vault, k8s Secrets)
3. Study encryption at rest
4. Explore security scanning (Snyk, Trivy)
5. Learn about security incident response

## Additional Resources

- SSH best practices: https://man.openbsd.org/ssh
- OWASP: https://owasp.org/www-project-top-ten/
- TLS: https://en.wikipedia.org/wiki/Transport_Layer_Security
- OpenSSL: https://www.openssl.org/docs/

