# 14 - Security Basics Cheatsheet

## SSH Key Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ssh-keygen -t rsa -b 4096` | Generate key pair | Creates RSA keys |
| `ssh-copy-id -i key user@host` | Install public key | Enable key auth |
| `ssh -i key user@host` | Connect with key | Use specific key |
| `ssh-keygen -l -f key` | Show fingerprint | Verify key |
| `ssh -v` | Verbose output | Debug connection |

## File Permission Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `chmod 644 file` | Set permissions | rw-r--r-- |
| `chmod u+x file` | Add execute (owner) | rwxr--r-- |
| `chmod -R 755 dir` | Recursive | All files in dir |
| `chown user:group file` | Change owner | New owner |
| `chgrp group file` | Change group | New group |
| `stat file` | Show details | All file info |

## Permission Modes Reference

| Mode | Format | Use Case |
|------|--------|----------|
| 400 | r------- | Private key |
| 600 | rw------ | Private file |
| 644 | rw-r--r-- | Regular file |
| 700 | rwx------ | Private directory |
| 755 | rwxr-xr-x | Executable/directory |
| 777 | rwxrwxrwx | Everyone (bad!) |

## User and Group Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `useradd username` | Create user | New account |
| `usermod -aG group user` | Add to group | User → group |
| `userdel username` | Delete user | Remove account |
| `groupadd groupname` | Create group | New group |
| `groups username` | List groups | User's groups |
| `id username` | User details | ID and groups |
| `passwd username` | Set password | Change password |

## Encryption Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `openssl genrsa 2048` | Generate RSA key | Private key |
| `openssl req -new` | Create CSR | Certificate request |
| `openssl x509 -in cert` | View cert | Show details |
| `openssl s_client -connect host:443` | Check cert | TLS handshake |
| `echo -n text \| sha256sum` | Hash text | SHA256 hash |
| `openssl passwd -1` | Hash password | crypt format |

## Certificate Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `openssl x509 -text -in cert.pem` | Show certificate | Full details |
| `openssl x509 -dates -in cert.pem` | Check expiry | Valid dates |
| `openssl x509 -issuer -in cert.pem` | Show issuer | CA name |
| `openssl verify cert.pem` | Verify cert | Check validity |
| `openssl s_client -showcerts` | Get cert chain | All CAs |

## Firewall Commands (ufw)

| Command | Purpose | Example |
|---------|---------|---------|
| `sudo ufw enable` | Turn on firewall | Enable protection |
| `sudo ufw allow 22/tcp` | Allow port | SSH access |
| `sudo ufw deny 23/tcp` | Block port | Block telnet |
| `sudo ufw delete allow 22` | Remove rule | Delete allow |
| `sudo ufw status` | Show rules | Current rules |
| `sudo ufw status verbose` | Detailed | Per-interface |

## Firewall Commands (iptables)

| Command | Purpose | Example |
|---------|---------|---------|
| `iptables -A INPUT -p tcp --dport 22 -j ACCEPT` | Allow port | SSH |
| `iptables -A INPUT -p tcp --dport 23 -j DROP` | Drop port | Telnet |
| `iptables -L` | List rules | All rules |
| `iptables -F` | Flush rules | Clear all (careful!) |
| `iptables-save` | Save rules | Persist |

## Authentication Methods

| Method | Security | Use Case |
|--------|----------|----------|
| Password | Low | Basic |
| SSH key | High | Automation |
| Certificate | High | TLS/HTTPS |
| MFA | Highest | Sensitive |
| OAuth2 | Medium | Web apps |

## Encryption Types

| Type | Speed | Use Case | Example |
|------|-------|----------|---------|
| Symmetric | Fast | Data | AES-256 |
| Asymmetric | Slow | Keys | RSA-2048 |
| Hash | Fast | Passwords | SHA256, bcrypt |
| TLS/SSL | Medium | Transport | HTTPS |

## Secure Defaults

```bash
# SSH
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
X11Forwarding no

# File permissions
umask 077           # New files: 600
chmod 644 files     # Regular files
chmod 755 dirs      # Directories
chmod 600 keys      # Private keys

# Passwords
12+ characters
Mixed case, numbers, symbols
No dictionary words
```

## Vulnerability Scanning

| Tool | Purpose | Use |
|------|---------|-----|
| `nmap` | Port scanning | Open services |
| `ssh-audit` | SSH config | Security check |
| `debsecan` | Debian vulns | Outdated packages |
| `clamav` | Malware | Virus scanning |
| `lynis` | System audit | General scan |

## Security Checklist

```
SSH:
☐ Keys: 4096-bit RSA
☐ Private: chmod 600
☐ Disable password auth
☐ Disable root login

Files:
☐ configs: 644
☐ scripts: 755
☐ keys: 600
☐ secrets: 600

Users:
☐ Strong passwords
☐ Least privilege
☐ Regular audits
☐ Remove unused

Firewall:
☐ Default deny
☐ SSH allowed
☐ Services whitelisted
☐ Log enabled

Updates:
☐ OS patched
☐ Apps current
☐ Kernel latest
☐ Dependencies updated
```

## Quick Commands

```bash
# Generate keys
ssh-keygen -t rsa -b 4096

# Set permissions
chmod 600 private_key
chmod 644 public_key

# Copy key to server
ssh-copy-id -i key user@host

# Check file perms
ls -la filename
stat filename

# Hash password
echo "password" | sha256sum

# Check SSL cert
openssl s_client -connect host:443

# Show firewall
sudo ufw status

# View user info
id username
groups username
```
