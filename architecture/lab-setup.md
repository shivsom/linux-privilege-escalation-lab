# Lab Architecture and Environment Setup

## Overview
This laboratory environment is designed as a controlled Linux privilege escalation testbed.  
The attacker begins with valid SSH access as a low-privileged user and attempts to escalate privileges through misconfigurations present on the target system.

## Host Environment
- Host Operating System: Windows 11
- Hypervisor: VMware Workstation Pro

## Virtual Machines

### Attacker Machine
- Operating System: Kali Linux 2025.3
- Role: External attacker
- Usage: SSH client and exploitation host

### Target Machine
- Operating System: Ubuntu Server 22.04 LTS
- Role: Vulnerable system under assessment
- Initial Access Vector: SSH login as a low-privileged user

## Network Configuration
- Network Mode: Host-only
- Rationale: Ensures isolation from external networks while allowing direct attacker–target interaction

## User Roles on Target System
The target system contains multiple user accounts to simulate a realistic multi-user server environment.

- `dev`  
  - Privilege Level: Low  
  - Purpose: Initial foothold for the attacker

- `ops`  
  - Privilege Level: sudo access  
  - Purpose: Simulates an operational user (not used during initial exploitation)

- `vic`  
  - Privilege Level: sudo access  
  - Purpose: Represents an administrative power user

## Services in Scope
Only services relevant to the attack surface are considered within the scope of this lab.

- `ssh.service`  
  - Used for initial access

- `cron.service`  
  - Present to simulate scheduled administrative tasks

- `apache2.service`  
  - Installed to reflect a realistic server environment (not directly exploited in this lab)

All other default system services (e.g., dbus, systemd components) are considered out of scope and are not analyzed.

## Assumptions and Scope
- The attacker does not possess sudo privileges at the start
- No kernel exploits are used
- The focus is on misconfiguration-based privilege escalation
- The lab is intentionally vulnerable and designed for educational and research purposes
