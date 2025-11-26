
# 🛡️ **Linux Security Hardening Log**

### **User Isolation • SSH Hardening • Auditd Monitoring • Firewall Baseline**

**Date:** 16 Nov 2025
**Analyst:** RoninBane
**System:** SpecterNode

---

## 🚀 **1. Purpose of This Hardening**

This system is occasionally shared by multiple users. The goal was to **isolate accounts**, **lock down SSH**, and **enable full auditing and monitoring**.

### **Objectives**

* Create a secure isolated user
* Harden SSH (key-only login, strict permissions)
* Enable system-wide audit logging
* Test remote access via mobile SSH (ConnectBot)
* Apply a basic firewall baseline
* Understand session lifecycle + service behavior

👉 **In short:** *Lock it down. Control it. Monitor everything.*

---

## 🧠 **2. Threat Model (Why This Matters)**

| Threat                               | Mitigation                               |
| ------------------------------------ | ---------------------------------------- |
| Unauthorized login attempts          | Dedicated user + restrictive permissions |
| Brute-force SSH attacks              | Key-based auth + limited login attempts  |
| Persistent unauthorized SSH sessions | `auditd` + session termination commands  |
| Misconfigurations weakening security | Manual verification + audit logs         |
| Abuse of running sessions            | Understanding SSH lifecycle + controls   |

---

## 🔧 **3. Actions Performed (Technical Summary)**

### ✔ 3.1 **Created a Secure Dedicated User**

```bash
sudo adduser reapernode
```

### ✔ 3.2 **Set Up SSH Directory & Key Auth**

```bash
sudo mkdir /home/reapernode/.ssh
sudo nano /home/reapernode/.ssh/authorized_keys
sudo chown -R reapernode:reapernode /home/reapernode/.ssh
sudo chmod 700 /home/reapernode/.ssh
sudo chmod 600 /home/reapernode/.ssh/authorized_keys
```

### ✔ 3.3 **Hardened SSH Configuration**

Key changes in **/etc/ssh/sshd_config**:

* Disabled root login
* Enforced StrictModes
* Forced key-based auth
* Reduced auth attempts
* Improved session rules

Apply changes:

```bash
sudo systemctl restart ssh
```

---

## 📱 **4. Mobile Test — ConnectBot Validation**

Tested SSH login **from Android** using ConnectBot.

Steps:

1. Install ConnectBot
2. Add host → `192.168.77.35`
3. Username → `reapernode`
4. Select **Use SSH key**
5. Connect

Mobile SSH behaves exactly like desktop SSH once connected — reliable and consistent.

---

## 🏠 **5. SSH Session Lifecycle (Critical Behavior)**

**Observation:**
SSH session stayed active even after running:

```bash
sudo systemctl stop ssh
```

**Analogy:**

> *You entered the house; then someone locked the door from outside — but you're already inside. Others can’t enter, but you won’t be kicked out.*

To forcefully disconnect a user:

```bash
sudo pkill -u reapernode
```

or

```bash
sudo kill <PID>
```

---

## 📜 **6. Enabled and Verified Auditd**

Check status:

```bash
sudo systemctl status auditd
```

Logs observed for:

* Logins
* Privilege escalation
* Command executions
* Sensitive file access

Useful commands:

```bash
sudo ausearch -m user-login
sudo ausearch -m USER_CMD
sudo less /var/log/audit/audit.log
```

---

## 🔥 **7. Firewall (UFW) Baseline**

```bash
sudo ufw allow ssh
sudo ufw enable
sudo ufw status verbose
```

---

## ✔ **8. Validation & Hardening Checks**

| Test                                        | Result         |
| ------------------------------------------- | -------------  |
| SSH key auth working                        | ✅             |
| Password auth disabled                      | ✅             |
| Root login blocked                          | ✅             |
| Correct folder permissions (700/600)        | ✅             |
| auditd logs sudo events                     | ✅             |
| SSH session persists after stopping service | 🟦 Expected    |
| Mobile SSH stable                           | ✅             |
| Firewall allows only SSH                    | ⚠️ (Baseline)  |

---

## 📈 **9. Hardening Score (Self-Assessment)**

| Area                 | Score    |
| -------------------- | -------- |
| User Isolation       | **9/10** |
| SSH Hardening        | **8/10** |
| Logging / Audit      | **7/10** |
| Firewall Baseline    | **6/10** |
| Validation & Testing | **9/10** |

**Next upgrades:**
Fail2Ban, custom SSH port, explicit audit rules, automated playbooks.

---

## ⚙️ **10. Future Automation – Setup Script (Draft)**

```bash
#!/bin/bash
# Basic hardening automation script (WIP)

adduser reapernode

mkdir -p /home/reapernode/.ssh
chmod 700 /home/reapernode/.ssh
touch /home/reapernode/.ssh/authorized_keys
chmod 600 /home/reapernode/.ssh/authorized_keys
chown -R reapernode:reapernode /home/reapernode/.ssh

ufw allow ssh
ufw enable

systemctl enable auditd
systemctl restart auditd

