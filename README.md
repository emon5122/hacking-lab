# 🎯 Hacking Lab Environment

A comprehensive, containerized penetration testing lab with realistic network segmentation, vulnerable applications, and CTF challenges.

**Perfect for:** Security researchers, pentesters, students, CTF players, and anyone learning offensive security.

## 📁 Structure

```
hacking-lab/
├── web-apps/          # Vulnerable web applications
├── network-targets/   # Network service targets (SSH, FTP)
├── privesc/           # Privilege escalation targets
├── advanced-targets/  # AD, Docker escapes, CVE targets, CTF flags
├── databases/         # 6 vulnerable database systems
├── api-targets/       # Modern API vulnerabilities
├── cicd-targets/      # CI/CD pipeline security
└── docker-compose.yml # Master compose file (runs all services)
```

## 🌐 Network Architecture

Services are **segmented into isolated networks** to simulate real environments:

- **DMZ Network** (172.20.0.0/24) - Public web apps
- **Internal Network** (172.21.0.0/24) - Databases and backend
- **API Network** (172.22.0.0/24) - API services
- **Vuln Network** (172.25.0.0/24) - Exploitable targets
- **Admin Network** (172.24.0.0/24) - Crown jewels (AD, flags)
- **CI/CD Network** (172.23.0.0/24) - Build systems

**Pivoting required!** Not all networks can talk to each other directly.

## 🚀 Quick Start

### Run All Services
```bash
docker-compose up -d
```

### Run Specific Category
```bash
cd web-apps && docker-compose up -d
cd network-targets && docker-compose up -d
cd privesc && docker-compose up -d
cd advanced-targets && docker-compose up -d
```

## 🌐 Web Applications

| Service | Port | URL | Description |
|---------|------|-----|-------------|
| DVWA | 8081 | http://localhost:8081 | Damn Vulnerable Web Application |
| Juice Shop | 8082 | http://localhost:8082 | OWASP Juice Shop |
| bWAPP | 8083 | http://localhost:8083 | Buggy Web Application |
| WebGoat | 8084 | http://localhost:8084/WebGoat | OWASP WebGoat |
| Mutillidae | 8085 | http://localhost:8085 | OWASP Mutillidae II |

## 🔌 Network Targets

| Service | Port | Credentials | Description |
|---------|------|-------------|-------------|
| SSH | 2222 | root:root | Vulnerable SSH server |
| FTP | 2121 | ftpuser:ftp123 | Vulnerable FTP server |

## 🎯 Privilege Escalation

| Service | Ports | Description |
|---------|-------|-------------|
| Metasploitable2 | 8021 (FTP)<br>8022 (SSH)<br>8086 (HTTP) | Multiple privilege escalation vectors |

## 🚀 Advanced Targets

| Service | Ports | Description |
|---------|-------|-------------|
| Active Directory | 389, 636, 88 | Full AD environment with DC and client |
| Docker Escape | - | Container breakout challenges |
| Struts RCE | 8090 | CVE-2017-5638 Apache Struts |
| Log4Shell | 8091 | CVE-2021-44228 Log4j2 RCE |
| Spring4Shell | 8092 | Spring Data Commons RCE |
| CTF Flags | - | 4 progressive flags (100-1000 pts) |

## 📊 Database Targets

| Service | Port | Credentials | Type |
|---------|------|-------------|------|
| MongoDB | 27017 | admin:password | NoSQL injection |
| Redis | 6379 | weakpass | RCE potential |
| PostgreSQL | 5432 | postgres:postgres | SQL injection |
| MySQL | 3307 | root:root | SQL attacks |
| Elasticsearch | 9200 | none | Data exposure |
| CouchDB | 5984 | admin:admin | Admin exploits |

## 🔌 API Targets

| Service | Port | Type | Vulnerabilities |
|---------|------|------|-----------------|
| Pixi | 8096 | REST | OWASP API Top 10 |
| crAPI | 8097 | REST | BOLA, BFLA, SSRF |
| DVGA | 8098 | GraphQL | Injection, DoS |
| REST API Goat | 8099 | REST | Full API security |
| Kong Gateway | 8100/8101 | Gateway | Misconfigurations |

## 🚀 CI/CD Targets

| Service | Port | Credentials | Purpose |
|---------|------|-------------|---------|
| Jenkins | 8110 | (see container) | Script console RCE |
| Gitea | 8111 | (configure) | Git service |
| GitLab | 8112 | root:Passw0rd! | Pipeline injection |
| Drone CI | 8114 | - | Pipeline security |
| Docker Registry | 5000 | none | Image tampering |
| SonarQube | 8115 | admin:admin | Code exposure |

---

## 🌐 Network Architecture & Pivoting

### Network Segmentation

Services are isolated into different networks simulating real environments:

```
┌─────────────────────────────────────────────────┐
│  DMZ (172.20.0.0/24)                            │
│  → Public web apps, CVE targets                 │
│  → Entry point for attacks                      │
└──────────────┬──────────────────────────────────┘
               │ Limited Access
┌──────────────┴──────────────────────────────────┐
│  Internal (172.21.0.0/24)                       │
│  → Databases, backend services                  │
│  → Requires pivoting from DMZ                   │
└──────────────┬──────────────────────────────────┘
               │ Restricted
┌──────────────┴──────────────────────────────────┐
│  Admin (172.24.0.0/24)                          │
│  → Active Directory, Docker escape, Flags       │
│  → Crown jewels - final objective               │
└─────────────────────────────────────────────────┘

Isolated Networks:
• API Network (172.22.0.0/24) - API services
• Vuln Network (172.25.0.0/24) - SSH/FTP targets  
• CI/CD Network (172.23.0.0/24) - Build systems
```

### Attack Paths

**Path 1: Web → Internal → Admin**
1. Exploit web app in DMZ (SQL injection)
2. Pivot through Log4Shell (dual-homed)
3. Access internal databases
4. Extract AD credentials
5. Compromise Domain Controller

**Path 2: Vuln → Internal → Admin**
1. Brute force SSH (vuln_network)
2. Pivot via Metasploitable2 bridge
3. Lateral movement to internal
4. Reach admin network

**Path 3: Direct Container Escape**
1. Compromise Jenkins (privileged)
2. Exploit Docker socket
3. Container breakout to host
4. Access all networks

### Pivoting Techniques

**SSH Tunneling:**
```bash
ssh -L 3306:172.21.0.5:3306 root@localhost -p 2222
```

**ProxyChains:**
```bash
ssh -D 1080 root@localhost -p 2222
proxychains nmap -sT 172.21.0.0/24
```

**Metasploit Routing:**
```bash
meterpreter > run autoroute -s 172.21.0.0/24
```

---

## 🎮 CTF Challenges

Capture 4 progressive flags worth 1,000 points total:

1. **FLAG 1** (100 pts) - Web exploitation in DMZ
2. **FLAG 2** (200 pts) - Credential discovery & reuse
3. **FLAG 3** (500 pts) - Container escape & privilege escalation
4. **FLAG 4** (1000 pts) - Active Directory domain takeover

Each flag contains hints for the next objective and tells a story of network compromise.

---

## 🚀 Quick Start

### Prerequisites
- Docker & Docker Compose
- 8GB+ RAM recommended
- 50GB disk space

### Start All Services
```bash
docker compose up -d
```

### Start Individual Categories
```bash
cd web-apps && docker compose up -d
cd databases && docker compose up -d
cd api-targets && docker compose up -d
cd cicd-targets && docker compose up -d
```

### Check Status
```bash
docker compose ps
docker compose logs -f <service_name>
```

### Stop Everything
```bash
docker compose down
docker compose down -v  # Also remove volumes
```

---

## 🎓 Learning Path

### Beginner
- Start with DVWA (set security to low)
- Practice SQL injection, XSS, command injection
- Try basic SSH/FTP brute forcing
- Explore database default credentials

### Intermediate
- Exploit real CVEs (Struts, Log4Shell)
- Practice API vulnerabilities (BOLA, injection)
- Set up SSH tunnels for pivoting
- Kerberoast Active Directory

### Advanced
- Complete full attack chain (DMZ → Admin)
- Container escape via Docker socket
- Golden Ticket attacks in AD
- CI/CD pipeline compromise
- Supply chain attacks

---

## ⚠️ Security Warnings

### DO NOT:
- ❌ Expose these services to the internet
- ❌ Run on production networks
- ❌ Use these credentials anywhere else
- ❌ Leave services running when not in use

### DO:
- ✅ Use only in isolated lab environments
- ✅ Run on localhost or VM
- ✅ Stop services after practice
- ✅ Use for educational purposes only
- ✅ Practice ethical hacking

### Legal Notice
These tools are for **educational purposes only**. Only test systems you own or have written permission to test. Unauthorized access to computer systems is illegal.

---

## 🛠️ Troubleshooting

### Images Won't Pull
All images have been validated. If you encounter issues:
```bash
docker compose config --images  # List all images
docker compose pull             # Pull all images
```

### Service Won't Start
```bash
docker compose logs <service>   # Check logs
docker compose restart <service> # Restart specific service
```

### Network Issues
Verify network isolation:
```bash
docker exec lab_dvwa ping lab_pixi          # Should FAIL (different networks)
docker exec lab_metasploitable2 ping <ip>  # Should work (dual-homed)
```

### Resource Issues
Reduce running services or increase Docker resources:
```bash
docker stats  # Monitor resource usage
```

---

## 📚 Resources & Learning

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP API Security Top 10](https://owasp.org/www-project-api-security/)
- [HackTricks](https://book.hacktricks.xyz/)
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
- [GTFOBins](https://gtfobins.github.io/)
- [Active Directory Attack Cheatsheet](https://github.com/S1ckB0y1337/Active-Directory-Exploitation-Cheat-Sheet)

---

## 🤝 Contributing

Found an issue? Have a suggestion? Feel free to:
- Open an issue
- Submit a pull request
- Share your attack writeups

---

## 📝 Usage Tips

- Use `docker compose ps` to check running services
- Use `docker compose logs <service>` to view logs
- Use `docker compose down` to stop all services
- Use `docker compose down -v` to remove volumes too
