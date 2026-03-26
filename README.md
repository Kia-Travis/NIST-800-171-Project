# NIST SP 800-171 CUI Linux Server — ISSO Portfolio Project

![Status](https://img.shields.io/badge/Status-Complete-brightgreen)
![Framework](https://img.shields.io/badge/Framework-NIST%20SP%20800--171%20Rev%202-blue)
![Controls](https://img.shields.io/badge/Controls%20Implemented-12-blue)
![OS](https://img.shields.io/badge/OS-Ubuntu%20Server%2024.04%20LTS-orange)

---

## Why I Built This

I'm currently working in a Tier 2 Service Desk role at a DoD contractor and working toward an ISSO position. I built this project because I didn't want to walk into my first security role learning everything from scratch.

I wanted to get my hands dirty — to actually implement security controls, break things, fix them, and document everything the way a real ISSO would. NIST SP 800-171 is the primary standard for protecting Controlled Unclassified Information (CUI) in DoD contractor environments, and being able to speak to it from hands-on experience rather than just a study guide is the difference I'm trying to make.

This project gave me that. It also reminded me how much I genuinely enjoy this work — which doesn't hurt either.

---

## Project Overview

This project demonstrates end-to-end ISSO competencies by implementing 12 NIST SP 800-171 security controls on a simulated CUI-handling Linux server environment, conducting security control validation testing, and producing a complete compliance documentation package.

The system simulates a small business network in a DoD ecosystem — processing, storing, and protecting Controlled Unclassified Information in accordance with NIST SP 800-171 Rev 2 requirements.

---

## Environment

| Component | Details |
|---|---|
| **Host Machine** | MacBook Pro — Apple Silicon M1 |
| **Virtualization** | UTM on Apple Silicon |
| **Operating System** | Ubuntu Server 24.04 LTS (ARM64) |
| **Hostname** | projecta |
| **IP Address** | 192.168.64.2 |
| **Network** | Private NAT — 192.168.64.0/24 |
| **Storage** | 20 GB virtual disk |
| **Architecture** | ARM64 (aarch64) |

---

## Security Controls Implemented

12 controls across 5 NIST SP 800-171 control families:

### Access Control (AC)
| Control | Name | Implementation |
|---|---|---|
| 3.1.1 | Limit System Access to Authorized Users | Created sensitive-data group; enforced role-based access with 4 user accounts (admin, user, auditor) using principle of least privilege |
| 3.1.8 | Limit Unsuccessful Login Attempts | Configured pam_faillock via /etc/pam.d/common-auth — account lockout after 3 failed attempts for 30 minutes |
| 3.1.12 | Monitor and Control Remote Access | Hardened sshd_config: disabled PasswordAuthentication, enabled PubkeyAuthentication, disabled PermitRootLogin |

### Identification and Authentication (IA)
| Control | Name | Implementation |
|---|---|---|
| 3.5.3 | Use Multifactor Authentication | Generated ed25519 SSH key pair; deployed public key to ~/.ssh/authorized_keys (chmod 600); secured ~/.ssh (chmod 700) |
| 3.5.7 | Enforce Password Complexity | Configured libpam-pwquality: 14-char minimum, digit, uppercase, lowercase, and special character required |
| 3.5.8 | Prohibit Password Reuse | Configured /etc/login.defs: 90-day max age, 1-day min age, 14-day expiration warning |

### Audit and Accountability (AU)
| Control | Name | Implementation |
|---|---|---|
| 3.3.1 | Create and Retain Audit Logs | Installed and enabled auditd (systemctl enable auditd); 40+ custom rules in /etc/audit/rules.d/isso-audit.rules |
| 3.3.2 | Ensure Audit Log Accountability | Rules monitor: auth events, account modifications, SSH config changes, sudo usage, file deletions, privileged commands, CUI access |

### System and Communications Protection (SC)
| Control | Name | Implementation |
|---|---|---|
| 3.13.1 | Monitor and Control Communications | UFW firewall: default-deny-incoming, permit SSH TCP/22 only, verified with ufw status verbose |
| 3.13.8 | Implement Cryptographic Mechanisms | SSH with ed25519 enforces encrypted transmission for all remote access sessions |

### Media Protection (MP)
| Control | Name | Implementation |
|---|---|---|
| 3.13.10 | Protect CUI at Rest | GPG AES-256 symmetric encryption applied to all CUI files; plaintext originals destroyed using shred -u |
| 3.13.16 | Protect CUI at Rest (Backup) | ~/sensitive-data/ directory holds only encrypted .gpg files; no plaintext CUI retained on disk |

---

## Security Control Validation Testing

Controls were not just implemented — they were tested and validated.

| Test | Control | Procedure | Result |
|---|---|---|---|
| T-01 | 3.3.1 Audit Logging | Ran `sudo cat /etc/passwd > /dev/null`; verified capture via `ausearch -ts recent` | ✅ PASS |
| T-02 | 3.13.1 Firewall | Attempted connection to TCP/23 (Telnet) via `nc -zv 192.168.64.2 23` | ✅ PASS — Connection refused |
| T-03 | 3.1.8 Account Lockout | SSH as alice with wrong password 3 consecutive times | ✅ PASS — Account locked |

---

## User Accounts and Roles

Role-based access control with segregation of duties:

| Username | Role | Group | Access |
|---|---|---|---|
| sassysyntax | System Administrator | sudo | Full admin; primary account for control implementation |
| alice | Standard User | sensitive-data | Read-only CUI access; no admin privileges |
| bob | Secondary Administrator | sudo | Backup admin; sudo elevated privileges |
| charlie | Auditor | none | Read-only audit log access; cannot access CUI or modify system |

---

## Documentation Package

| Document | Filename | Description |
|---|---|---|
| **System Security Plan (SSP)** | `SSP.pdf` | Full SSP documenting all 12 controls across 5 families, system architecture, roles, and testing results — aligned to NIST SP 800-171 Rev 2 |
| **Plan of Action & Milestones (POA&M)** | `POAM_ProjectA.pdf` | 5 tracked open items with risk assessments, remediation plans, and target dates |
| **Project Implementation Guide** | `NIST 800-171 Implementation Project.pdf` | Step-by-step implementation guide with terminal screenshots and evidence artifacts |

---

## Plan of Action & Milestones (POA&M) Summary

Identified gaps tracked for remediation — because honest self-assessment is part of the job:

| # | Finding | Risk Level | Target Date |
|---|---|---|---|
| 1 | No continuous monitoring / SIEM integration | Medium | May 31, 2026 |
| 2 | No automated patch management | Medium | May 31, 2026 |
| 3 | No centralized log storage / forwarding | Medium | May 31, 2026 |
| 4 | No formal incident response plan | Low | May 31, 2026 |
| 5 | No formal configuration management process | Moderate | May 31, 2026 |

---

## Skills Demonstrated

**Frameworks & Standards**
- NIST SP 800-171 Rev 2
- NIST SP 800-53 Rev 5
- NIST SP 800-37 Rev 2 (RMF concepts)
- FIPS 199

**Security Domains**
- Access Control & Identity Management
- Audit & Accountability
- Network Security & Firewall Configuration
- Cryptography & Data Protection at Rest
- Security Assessment & Validation
- Compliance Documentation

**Technical Tools**
- Linux System Administration (Ubuntu Server 24.04)
- OpenSSH / ed25519 key authentication
- PAM (pam_faillock, libpam-pwquality)
- Linux Audit Framework (auditd)
- UFW (Uncomplicated Firewall)
- GPG / AES-256 encryption
- GNU shred for secure deletion
- UTM virtualization on Apple Silicon

**Documentation**
- System Security Plan (SSP)
- Plan of Action & Milestones (POA&M)
- Security control implementation narratives
- Evidence package with terminal screenshots

---

## Repository Structure

```
NIST-800-171-Project/
├── README.md                                    ← You are here
├── SSP.pdf                                      ← System Security Plan
├── POAM_ProjectA.pdf                            ← Plan of Action & Milestones
├── NIST 800-171 Implementation Project.pdf      ← Step-by-step implementation guide
├── isso-audit.rules                             ← Custom auditd ruleset
├── baseline-assessment.png                      ← Baseline security assessment output
├── ssh-key-auth.png                             ← SSH key authentication validation
├── ufw-status.png                               ← UFW firewall configuration
├── auditd-rules.png                             ← Loaded audit rules verification
├── account-lockout-test.png                     ← Account lockout policy test
└── encrypted-files.png                          ← Encrypted CUI files in sensitive-data/
```

---

## About Me

IT professional with hands-on DoD environment experience actively transitioning into cybersecurity, targeting ISSO and GRC roles in the Huntsville defense contracting space.

- CompTIA Security+ | CompTIA CySA+
- Tier 2 Service Desk — DoD Contractor Environment
- Huntsville, AL — Redstone Arsenal ecosystem

---

*This project is a portfolio demonstration of ISSO competencies. All system names, data, and personnel are fictional and created solely for educational purposes.*
