# Linux Security Hardening Lab (SOC-Oriented)

## Overview

This project demonstrates practical Linux system hardening aligned with Security Operations Center (SOC) responsibilities. The focus is on securing remote access, enforcing least privilege, and enabling audit-based visibility for detection and investigation.

---

## Key Implementations

**User Isolation**

* Created a dedicated non-root user (`reapernode`)
* Enforced strict ownership and permission controls

**SSH Hardening**

* Disabled root login
* Enforced key-based authentication only
* Applied strict permission validation
* Limited authentication attempts

**Audit Logging**

* Enabled `auditd` for system-wide monitoring
* Captured login activity, privilege escalation, and command execution

**Firewall Baseline**

* Configured UFW to allow only SSH traffic
* Reduced unnecessary network exposure

**Session Management**

* Analyzed SSH session persistence behavior
* Implemented forced session termination techniques

---

## SOC Relevance

* Detection of unauthorized login attempts
* Monitoring of privilege escalation and command execution
* Understanding of attacker persistence through active sessions
* Reduction of external attack surface via hardened SSH configuration

---

## Validation

* Key-based SSH authentication enforced
* Password authentication disabled
* Root login successfully blocked
* Audit logs verified for critical events
* Firewall active with restricted access

---

## Limitations and Future Enhancements

* No intrusion prevention (e.g., Fail2Ban)
* Limited audit rule customization
* No centralized logging or SIEM integration

**Planned Improvements**

* Advanced auditd rules for detection use cases
* Integration with SIEM platforms (Splunk / ELK)
* Automated hardening via scripts or configuration management tools

---

## Conclusion

This lab reflects a foundational understanding of Linux hardening from a SOC perspective, emphasizing not only secure configuration but also visibility, monitoring, and attacker behavior awareness.
