# 14 - Security Basics: Cheatsheet

## Authentication vs Authorization

```
Authentication: WHO are you?
- Username/password
- Multi-factor auth
- OAuth, SAML

Authorization: WHAT can you do?
- Role-based access
- Attribute-based
- Permissions
```

## Encryption

| Type | Purpose | Example |
|------|---------|---------|
| **Symmetric** | Encrypt & decrypt same key | AES-256 |
| **Asymmetric** | Public/private key pairs | RSA, ECDSA |
| **Hashing** | One-way, fingerprinting | SHA-256, bcrypt |

## Common Vulnerabilities

```
OWASP Top 10:
1. Injection (SQL, NoSQL, OS)
2. Authentication bypass
3. Sensitive data exposure
4. XML External Entities (XXE)
5. Access control failures
6. Security misconfiguration
7. XSS (Cross-Site Scripting)
8. Insecure deserialization
9. Using components with known vulns
10. Insufficient logging/monitoring
```

## Security Commands

```bash
# Generate SSH key pair
ssh-keygen -t ed25519 -f mykey

# Encrypt file
openssl enc -aes-256-cbc -in file -out file.enc

# Hash password
echo -n password | sha256sum
# Better: Use bcrypt/argon2

# Check SSL certificate
openssl x509 -in cert.pem -text -noout

# Create self-signed cert
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
```

## Best Practices

```
- Use HTTPS everywhere
- Hash passwords (never plain text)
- Rotate keys regularly
- Least privilege principle
- Defense in depth
- Security headers (CORS, CSP, X-Frame-Options)
- Input validation/sanitization
- Output encoding
- Rate limiting
- Security logging
```

## Certificate Management

```bash
# Check expiration
openssl x509 -noout -dates -in cert.pem

# View certificate chain
openssl s_client -connect host:443 -showcerts

# Validate certificate
openssl verify cert.pem
```

---
