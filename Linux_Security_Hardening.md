# Linux Security Hardening Log

🛡️ Linux Security Hardening Log
User Isolation • SSH Configuration • Auditd Monitoring • Firewall Baseline

Date: 16 Nov 2025
Analyst: RoninBane
System: SpecterNode

🔍 1. Purpose of This Hardening

This laptop is used by multiple people occasionally. I needed to ensure strict isolation, secure remote access, and full auditing of system behavior.

Objectives:

Create a secure, isolated user

Harden SSH (key-only auth, strict permissions)

Enable system-wide audit logging

Validate behavior via mobile SSH (ConnectBot)

Apply basic firewall rules

Understand session lifecycle and service control

In short:
→ Lock down the system. → Control access. → Monitor everything.

🧠 2. Threat Model (Why These Controls Matter)
Threat	Mitigation
Unauthorized users accessing my environment	Dedicated user + restrictive home folder
Brute-force SSH login attempts	Key-based auth + limited auth attempts + UFW
Persistent unauthorized SSH sessions	auditd + process-level session termination
Hidden misconfigurations that quietly weaken security	Permission verification + audit logging
Attackers abusing running sessions	Understanding SSH session lifecycle
📝 3. Actions Performed (Technical Summary)
✔ 3.1 Created a Secure Dedicated User
sudo adduser reapernode

✔ 3.2 Set Up SSH Directory & Key Authentication
sudo mkdir /home/reapernode/.ssh
sudo nano /home/reapernode/.ssh/authorized_keys
sudo chown -R reapernode:reapernode /home/reapernode/.ssh
sudo chmod 700 /home/reapernode/.ssh
sudo chmod 600 /home/reapernode/.ssh/authorized_keys

✔ 3.3 Hardened SSH Configuration

Changes made to /etc/ssh/sshd_config:

Disabled root login

Enforced StrictModes

Forced key-based authentication

Reduced authentication tries

Limited session behaviour

Restarted the SSH service to apply:

sudo systemctl restart ssh

📱 4. Mobile Real-World Test — ConnectBot

Tested SSH from a phone (Android) to verify practical reliability.

Steps:

Install ConnectBot

Add host: 192.168.77.35

Username: reapernode

Select Use SSH key

Connect → validate interactive shell

Checked permissions, commands, and stability

Why This Matters

Mobile SSH clients behave exactly like desktop clients once the session is live.
This revealed the key behavior below.

🏠 5. SSH Session Lifecycle – Critical Behavior (Analogy)

You observed:

After connecting via SSH

You disabled the SSH service (sudo systemctl stop ssh)

Your session stayed alive.

Analogy:

“It’s like entering a house and then someone locks the front door behind you — you’re already inside, so locking the door stops others from entering but doesn’t boot you out.”

This is normal, expected SSH behavior.

To forcibly disconnect users:

sudo pkill -u reapernode


or

sudo kill <sshd-session-pid>

📜 6. Enabled and Verified Auditd

Checked status:

sudo systemctl status auditd


Configured audit rules to monitor:

Logins

Privilege escalation

Command executions

Sensitive file access

View logs:

sudo ausearch -m user-login
sudo ausearch -m USER_CMD
sudo less /var/log/audit/audit.log

🔥 7. Firewall (UFW) Configuration
sudo ufw allow ssh
sudo ufw enable
sudo ufw status verbose

✔ 8. Verification Tests (Hardening Validated)
Test	Result
SSH key authentication works	✅ PASS
Password login disabled	✅ PASS
Root login blocked	✅ PASS
Permission checks (700/600) enforced	✅ PASS
auditd logs sudo events	✅ PASS
SSH session persists after stopping sshd	🟦 Expected
Mobile SSH session stable	✅ PASS
Firewall allows only SSH	⚠️ Baseline (good for now)
📈 9. Hardening Score (Self-Assessment)
Area	Score
User Isolation	9/10
SSH Hardening	8/10
Logging / Audit	7/10
Firewall Baseline	6/10
Validation & Testing	9/10

Next upgrades: Fail2Ban, non-default SSH port, explicit audit rules.

⚙️ 10. Future Automation — Setup Script (Draft)
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


(You’ll refine this when creating the Ansible playbook.)
