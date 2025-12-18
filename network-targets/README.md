# Network Targets

Network service targets for practicing penetration testing techniques.

## 🎯 Services

### 1. Vulnerable SSH Server
- **Port:** 2222
- **Credentials:** root:root
- **Purpose:** Practice SSH attacks, brute-forcing, configuration analysis

**Connection:**
```bash
ssh root@localhost -p 2222
# Password: root
```

### 2. Vulnerable FTP Server
- **Port:** 2121 (Control), 21100-21110 (Passive)
- **Credentials:** ftpuser:ftp123
- **Purpose:** Practice FTP enumeration, anonymous access, file transfers

**Connection:**
```bash
ftp localhost 2121
# Username: ftpuser
# Password: ftp123
```

## 🛠️ Practice Scenarios

### SSH Target
- Brute force attack with Hydra
- Weak password exploitation
- SSH enumeration with nmap
- Banner grabbing

### FTP Target
- Anonymous FTP access testing
- Brute force FTP credentials
- FTP bounce attacks
- Directory traversal

## 💡 Tools to Use

- **nmap:** Service discovery and enumeration
- **Hydra:** Brute force attacks
- **Metasploit:** Exploitation framework
- **netcat:** Manual connections
- **Wireshark:** Traffic analysis

## 🚀 Usage

```bash
# Start all network services
docker-compose up -d

# Check service status
docker-compose ps

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

## 🔍 Scanning Examples

```bash
# Scan SSH service
nmap -p 2222 -sV localhost

# Scan FTP service
nmap -p 2121 -sV localhost

# Brute force SSH
hydra -l root -P /path/to/wordlist.txt ssh://localhost:2222

# Brute force FTP
hydra -l ftpuser -P /path/to/wordlist.txt ftp://localhost:2121
```
