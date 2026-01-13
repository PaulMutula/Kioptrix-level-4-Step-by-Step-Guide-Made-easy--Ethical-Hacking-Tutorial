# Kioptrix Level 4 Exploitation Walkthrough

**Author:** Paul Mutula
**Role:** Cybersecurity Analyst

![Linux](https://img.shields.io/badge/Linux-Kali-blue)
![Nmap](https://img.shields.io/badge/Tool-Nmap-success)
![Pentesting](https://img.shields.io/badge/Skill-Penetration%20Testing-red)
![CTF](https://img.shields.io/badge/Lab-CTF%20Practice-orange)

---

## ⚠️ Disclaimer

This walkthrough is strictly for **educational and ethical penetration testing purposes**. All activities described were performed in a **controlled lab environment** on intentionally vulnerable systems. Unauthorized testing of systems you do not own or have permission to test is illegal.

---

## 1. Lab Environment

* **Attacker Machine:** Kali Linux
* **Target Machine:** Kioptrix Level 4
* **Network Type:** Host-only / Internal Network
* **Objective:** Identify vulnerabilities, gain initial access, and escalate privileges to root

---

## 2. Reconnaissance

The reconnaissance phase focused on identifying live hosts and open services within the network.

### 2.1 Host Discovery

The following command was used to identify active hosts on the network:

```bash
netdiscover -i eth0
```

The Kioptrix target was identified with the IP address:

```
192.168.56.106
```

---

### 2.2 Port Scanning

An Nmap scan was performed to discover open ports and running services:

```bash
nmap -T4 -sS 192.168.56.106
```

📸 **Screenshot Placeholder**
`image/nmap_scan.png`

**Open ports discovered included:**

* 22 (SSH)
* 80 (HTTP)
* 139 (Samba)
* 445 (Samba)

These services were selected for further enumeration.

---

## 3. Enumeration

### 3.1 Web Application Enumeration

Accessing the target via a web browser revealed a login page hosted on the HTTP service:

```
http://192.168.56.106
```

---

### 3.2 SQL Injection Testing

Initial testing for SQL injection was conducted on the login form using a single quote (`'`). The application responded with an SQL error, confirming that it was vulnerable to SQL injection.

📸 **Screenshot Placeholders**

* `image/login_page.png`
* `image/sql_injection_error.png`

A login bypass was successfully achieved using basic SQL injection techniques, granting access to the application.

---

### 3.3 Directory Bruteforcing

To identify hidden directories and files, **Feroxbuster** was installed and executed:

```bash
sudo apt install feroxbuster
feroxbuster -u http://192.168.56.106
```

📸 **Screenshot Placeholder**
`image/feroxbuster_results.png`

Several interesting directories and PHP files were discovered, including user-related pages.

---

## 4. Credential Discovery

One of the discovered pages revealed credentials belonging to a user:

* **Username:** john
* **Password:** MyNameIsJohn

These credentials were reused to attempt access to other exposed services.

---

## 5. Exploitation

### 5.1 Samba Enumeration

The discovered credentials were tested against Samba services:

```bash
smbclient -L 192.168.56.106 -U john
```

Although access was achieved, no immediate shell was obtained through Samba.

---

### 5.2 SSH Access

The same credentials were successfully used to authenticate via SSH:

```bash
ssh john@192.168.56.106
```

📸 **Screenshot Placeholder**
`image/ssh_access_john.png`

This provided shell access as the user **john**.

---

## 6. Privilege Escalation

### 6.1 System Enumeration

After gaining access as *john*, system enumeration was performed to identify privilege escalation vectors.

### 6.2 Database Credential Extraction

Navigating through the web application files revealed database configuration details within `checklogin.php`:

* **Database Name:** members
* **Database User:** root
* **Password:** (null)

---

### 6.3 Database Access

Using the extracted credentials, access to the MySQL database was obtained:

```bash
mysql -u root members
```

This confirmed full database access and demonstrated poor credential management.

---

### 6.4 Root Access

Privilege escalation was completed by switching to the root user:

```bash
sudo su
```

Verification commands:

```bash
whoami
ls /
```

📸 **Screenshot Placeholders**

* `image/root_shell.png`
* `image/whoami_root.png`

Successful execution confirmed **root-level access**.

---

## 7. Proof of Compromise

* Root shell obtained
* Full filesystem access
* Database access with administrative privileges

---

## 8. Tools Used

* Nmap
* Netdiscover
* Feroxbuster
* MySQL
* SSH
* Samba

---

## 9. Conclusion

This lab demonstrated how **web application vulnerabilities**, **credential reuse**, and **weak database configurations** can be chained together to fully compromise a system. Proper input validation, strong credential policies, and service hardening are critical in preventing such attacks.

---

## 10. Key Learning Outcomes

* Importance of secure coding practices
* Risks of credential reuse
* Value of systematic enumeration
* Effective privilege escalation techniques

---

✅ **Status:** System successfully compromised in a controlled lab environment
