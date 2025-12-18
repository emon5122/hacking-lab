# Vulnerable Web Applications

This directory contains intentionally vulnerable web applications for security testing and learning.

## 🎯 Applications

### 1. DVWA (Damn Vulnerable Web Application)
- **Port:** 8081
- **URL:** http://localhost:8081
- **Default Credentials:** admin:password
- **Focus:** SQL Injection, XSS, CSRF, File Upload, Command Injection

### 2. OWASP Juice Shop
- **Port:** 8082
- **URL:** http://localhost:8082
- **Focus:** Modern web vulnerabilities, OWASP Top 10

### 3. bWAPP (Buggy Web Application)
- **Port:** 8083
- **URL:** http://localhost:8083
- **Default Credentials:** bee:bug
- **Focus:** 100+ vulnerabilities covering all OWASP Top 10

### 4. WebGoat
- **Port:** 8084
- **URL:** http://localhost:8084/WebGoat
- **Focus:** Interactive security lessons and challenges

### 5. Mutillidae II
- **Port:** 8085
- **URL:** http://localhost:8085
- **Focus:** OWASP Top 10 vulnerabilities with hints

## 🚀 Usage

```bash
# Start all web applications
docker-compose up -d

# Start specific application
docker-compose up -d dvwa

# View logs
docker-compose logs -f

# Stop all applications
docker-compose down
```

## 📚 Learning Path

1. **Beginners:** Start with DVWA (set security level to low)
2. **Intermediate:** Try Mutillidae or bWAPP
3. **Advanced:** Challenge yourself with Juice Shop
4. **Guided Learning:** Use WebGoat for structured lessons
