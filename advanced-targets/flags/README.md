# CTF Flags

Capture-the-flag style objectives for the hacking lab.

## 🏁 Flags

### Flag 1: Web Application Entry
**File:** flag1_web.txt  
**Flag:** `WEB{juicy_entry_point}`  
**Objective:** Successfully exploit a web application vulnerability  
**Hints:**
- Start with OWASP Top 10 vulnerabilities
- Check for SQL injection, XSS, or RCE
- Default credentials are your friend

### Flag 2: Credential Compromise
**File:** flag2_creds.txt  
**Flag:** `CREDS{password_reuse_wins}`  
**Objective:** Extract or crack credentials from a service  
**Hints:**
- Weak passwords are common
- Check default configurations
- Password reuse across services
- Don't forget database dumps

### Flag 3: Privilege Escalation
**File:** flag3_privesc.txt  
**Flag:** `ROOT{docker_is_not_a_vm}`  
**Objective:** Escalate to root/administrator privileges  
**Hints:**
- Docker misconfigurations
- SUID binaries
- Kernel exploits
- Container escape techniques

### Flag 4: Active Directory Compromise
**File:** flag4_ad.txt  
**Flag:** `AD{kerberoast_the_world}`  
**Objective:** Compromise the Active Directory environment  
**Hints:**
- Kerberoasting
- AS-REP Roasting
- LDAP enumeration
- Service account exploitation

## 🎯 Challenge Path

**Recommended Order:**
1. **Web Flag** → Start here, easiest entry point
2. **Credentials Flag** → Use web access to find creds
3. **Privilege Escalation** → Use creds to pivot and escalate
4. **AD Flag** → Final boss, requires multiple techniques

## 📊 Difficulty Ratings

| Flag | Difficulty | Estimated Time | Skills Required |
|------|-----------|----------------|-----------------|
| Web | ⭐ Easy | 15-30 min | Basic web exploitation |
| Creds | ⭐⭐ Medium | 30-60 min | Credential attacks, brute force |
| PrivEsc | ⭐⭐⭐ Hard | 1-2 hours | Linux/container security |
| AD | ⭐⭐⭐⭐ Expert | 2-4 hours | Windows/AD attacks |

## 🛠️ Tools You'll Need

- **Web:** Burp Suite, sqlmap, nikto
- **Creds:** Hydra, John the Ripper, Hashcat
- **PrivEsc:** LinPEAS, pspy, GTFOBins
- **AD:** Impacket, BloodHound, Rubeus, CrackMapExec

## ✅ Validation

Capture a flag by successfully accessing and reading the flag file. Submit flags in the format shown (including brackets and underscores).

**Example submission:**
```
WEB{juicy_entry_point}
```

## 🏆 Bonus Objectives

- **Speed Run:** Capture all flags in under 2 hours
- **Stealth Mode:** Minimize log footprint
- **Persistence:** Maintain access after initial compromise
- **Lateral Movement:** Pivot between containers
- **Full Chain:** Link all exploits in a single attack chain

Good luck, and happy hacking! 🎉
