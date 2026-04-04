# 🛡️ Linux Security Hardening Lab

## 🔥 Overview

This project demonstrates a **complete Linux system hardening workflow** on Fedora, focusing on **SSH security, user isolation, firewall control, and attack simulation with log validation**.

The goal was not just configuration — but **real-world validation by simulating attacks and analyzing system behavior**.

---

## 🎯 Objectives

* Enforce **secure remote access using SSH key-based authentication**
* Eliminate password-based attack vectors
* Restrict system exposure using firewall rules
* Monitor authentication activity using system logs
* Simulate attacker behavior and validate defenses

---

## 🧠 Threat Model

| Threat                  | Mitigation                       |
| ----------------------- | -------------------------------- |
| Brute-force SSH attacks | Disabled password authentication |
| Unauthorized access     | Key-based authentication only    |
| Root compromise         | Root login disabled              |
| Network exposure        | Firewall restrictions            |
| Unknown login attempts  | Log monitoring (journalctl)      |

---

## ⚙️ System Setup

* OS: Fedora KDE
* Host: SpecterNode
* User: ReaperNode

---

## 🔐 SSH Hardening

### Key-Based Authentication Setup

```bash
ssh-keygen -t rsa -b 4096
```

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

### SSH Configuration (`/etc/ssh/sshd_config`)

```bash
PermitRootLogin no
PasswordAuthentication no
KbdInteractiveAuthentication no
PubkeyAuthentication yes

MaxAuthTries 3
LoginGraceTime 30
AllowUsers ReaperNode
```

---

### Restart SSH

```bash
sudo systemctl restart sshd
```

---

## 📱 Remote Access Validation

* SSH tested from Android using ConnectBot
* Verified:

  * Key-based login works
  * Password login is fully blocked

---

## 🧪 Authentication Testing

### Password Attack Simulation

```bash
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no ReaperNode@localhost
```

✅ Result: `Permission denied (publickey)`

---

### Invalid User Attack

```bash
ssh fakeuser@localhost
```

---

### Brute Force Simulation

```bash
for i in {1..5}; do ssh fakeuser@localhost; done
```

---

## 📜 Log Monitoring

Real-time monitoring:

```bash
journalctl -u sshd -f
```

---

### Observed Logs

* `Invalid user fakeuser`
* `Connection closed [preauth]`
* `Accepted publickey`

👉 System rejects unauthorized users **before authentication stage**

---

## 🔥 Firewall Hardening (firewalld)

### Removed open SSH access

```bash
sudo firewall-cmd --remove-service=ssh --permanent
```

---

### Restricted SSH to specific IP

```bash
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="<trusted-ip>" port port="22" protocol="tcp" accept'
```

---

### Reload firewall

```bash
sudo firewall-cmd --reload
```

---

### Result

* Only trusted device can access SSH
* All other devices blocked at network level

---

## 🧠 Key Security Concepts Applied

* Defense in Depth
* Least Privilege
* Attack Surface Reduction
* Pre-authentication rejection
* Network-level access control

---

## 💣 Key Findings

* Disabling password authentication eliminates brute-force risk
* SSH sessions persist even after service stop (session lifecycle insight)
* Network exposure must be controlled separately from authentication
* System logs provide clear visibility into attacker behavior

---

## 🚀 Outcome

Successfully built a **hardened Linux system** that:

* Accepts only key-based SSH authentication
* Blocks unauthorized access attempts early
* Restricts network-level access to trusted sources
* Provides clear audit visibility of all login attempts

---

## 📌 Future Improvements

* Fail2Ban (auto-block attackers)
* Custom auditd rules
* Port knocking / Zero Trust access
* Automated hardening scripts

---

## 🧠 Author

**RoninBane**
Cybersecurity & Systems Engineering
