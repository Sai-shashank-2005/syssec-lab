# 🛡️ Linux Security Hardening & Monitoring Lab

[![Role](https://img.shields.io/badge/Role-SOC%20Analyst-blue.svg)]()
[![Linux](https://img.shields.io/badge/OS-Fedora%20/%20Ubuntu-orange.svg)](https://www.linux.org/)
[![Security](https://img.shields.io/badge/Focus-System%20Hardening-red.svg)]()
[![Audit](https://img.shields.io/badge/Visibility-Auditd%20%2B%20Journalctl-blue.svg)]()

> **Overview**
> This lab demonstrates a comprehensive Linux system hardening workflow designed for a **Security Operations Center (SOC)** context. The project focuses on moving beyond basic configuration by implementing strict identity management, reducing the network attack surface, and validating defenses through simulated attack vectors and log analysis.

---

## 🎯 Key Objectives

* **Identity & Access Management (IAM):** Enforce least privilege via dedicated user isolation.
* **Remote Access Security:** Eliminate credential-based attacks by enforcing SSH Key-based authentication.
* **Attack Surface Reduction:** Implement host-based firewall rules to restrict ingress traffic.
* **Continuous Monitoring:** Configure `auditd` and `journalctl` to capture privilege escalation and unauthorized access attempts.
* **Validation:** Simulate brute-force and unauthorized login attempts to verify defensive posture.

---

## 🛠️ Technical Stack

| Category | Technologies Used |
| :--- | :--- |
| **Operating Systems** | `Fedora (SpecterNode)`, `Ubuntu` |
| **Core Tools** | `OpenSSH`, `UFW / Firewalld`, `Auditd`, `Journalctl` |
| **Remote Access** | `ConnectBot` (Mobile/Android validation) |
| **Security Concepts** | Defense in Depth, Least Privilege, Pre-authentication rejection |

---

## 🔐 Implementation Details

### 🔹 1. SSH Hardening & User Isolation
Created a dedicated non-root user (`reapernode`) and modified `/etc/ssh/sshd_config` to eliminate common attack vectors:
* **Disabled Root Login:** Prevented direct administrative access via SSH.
* **Public Key Authentication:** Forced `PubkeyAuthentication yes` and `PasswordAuthentication no`.
* **Session Control:** Set `MaxAuthTries 3` and `LoginGraceTime 30` to mitigate automated scripts.

### 🔹 2. Network Defense (Firewall)
Configured host-level protection to ensure only authorized nodes can communicate with the service:
* **UFW/Firewalld Baseline:** Denied all incoming traffic except for SSH.
* **Rich Rules:** Restricted SSH access to specific trusted source IPs to prevent lateral movement and external exposure.

### 🔹 3. Visibility & Auditing
Enabled system-wide monitoring to ensure every critical action is logged for forensic analysis:
* **Auditd:** Configured to track `USER_CMD` (sudo usage) and `user-login` events.
* **Log Verification:** Monitored `/var/log/audit/audit.log` and `journalctl -u sshd`.

---

## 🧪 Simulation & Validation

### Attack Scenarios
To verify the hardening, simulated several common attack patterns:

| Scenario | Tool / Command | Expected Result | Result |
| :--- | :--- | :--- | :--- |
| **Password Brute Force** | `ssh -o PreferredAuthentications=password` | Immediate Rejection | ✅ Success |
| **Invalid User Access** | `ssh fakeuser@localhost` | Pre-auth Rejection | ✅ Success |
| **Root Login Attempt** | `ssh root@<IP>` | Permission Denied | ✅ Success |

### Forensic Log Analysis
During simulation, the following logs were captured to validate SOC visibility:
* `Invalid user fakeuser`: Confirmed the system identifies unauthorized users.
* `Connection closed [preauth]`: Confirmed the firewall/SSH daemon drops connections before the handshake completes.
* `Accepted publickey`: Confirmed authorized access is properly attributed.

---

## 💡 Key Findings & Lifecycle Insights

* **Session Persistence:** Discovered that stopping the `sshd` service does not terminate active sessions. This highlights the need for manual session termination (`pkill -u`) or `TMOUT` configurations in a high-security environment.
* **Defense in Depth:** Even if the firewall is bypassed, the lack of password authentication provides a second, robust layer of defense.

---

## 🚀 Future Enhancements

- [ ] **Automated Hardening:** Develop Ansible Playbooks to deploy these configurations across multiple nodes.
- [ ] **Intrusion Prevention:** Integrate **Fail2Ban** to automatically null-route persistent attackers.
- [ ] **SIEM Integration:** Forward `auditd` logs to a centralized ELK or Splunk instance for real-time alerting.
- [ ] **Kernel Hardening:** Implement `sysctl` optimizations for network security.

---

## 👤 Author

**Sai Shashank P**
*SOC Analyst*
Dedicated to Threat Detection, Incident Response, and Security Engineering.
