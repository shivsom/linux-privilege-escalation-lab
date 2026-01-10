# Linux Privilege Escalation Lab

## Overview
This repository documents a controlled Linux privilege escalation lab designed to study how modern Linux systems handle privileged execution, why certain legacy exploitation techniques fail, and how misconfigured Linux capabilities can still lead to full root compromise.

The lab intentionally focuses on **local privilege escalation**, starting from a low-privileged SSH user and progressing through systematic assessment, failed exploit paths, and a successful capability-based escalation.

This project emphasizes **reasoning, verification, and adaptation**, rather than blind exploitation.

---

## Lab Objectives
- Evaluate common SUID-based privilege escalation techniques on a modern Ubuntu system
- Understand why classic SUID exploits often fail under current OS hardening
- Identify and exploit a real-world capability misconfiguration
- Analyze root causes behind both failed and successful escalation paths
- Propose concrete mitigation and hardening strategies

---

## Environment Summary
- **Attacker OS:** Kali Linux  
- **Target OS:** Ubuntu Server (modern LTS)  
- **Initial Access:** SSH as low-privileged user (`dev`)  
- **Scope:** Local privilege escalation only  
- **Out of Scope:** Network-based attacks, kernel exploits, post-exploitation

A detailed environment breakdown is available in `architecture/`.

---

## Repository Structure
```

linux-privilege-escalation-lab/  
├── README.md  
├── architecture/  
│ └── lab-setup.md  
├── exploit/  
│ ├── vulnerable-code/  
│ │ ├── vuln.c  
│ │ └── README.md  
│ ├── exploit-code/  
│ │ ├── capbash.c  
│ │ └── README.md  
│ └── walkthrough.md  
├── analysis/  
│ ├── suid-assessment.md  
│ ├── root-cause.md  
│ └── mitigation.md  
└── poc-video.md

```

---

## Key Findings

### SUID Assessment
A custom SUID-root binary was identified and analyzed.  
Despite exhibiting historically dangerous patterns (use of `system()`), multiple exploitation attempts failed.

This failure was not accidental. It reflects:
- Environment sanitization
- Reduced trust in inherited execution contexts
- Defensive defaults in modern Linux distributions

Detailed analysis is available in `analysis/suid-assessment.md`.

---

### Capability Abuse (Successful Escalation)
A custom binary (`capbash`) was discovered with the `cap_setuid` capability assigned.

This misconfiguration allowed:
- Arbitrary UID transition via `setuid(0)`
- Direct spawning of a root shell from a low-privileged context

This represents the **actual privilege escalation root cause** in the lab.

---

## Walkthrough
A structured attack narrative is provided in `exploit/walkthrough.md`, documenting:
- Initial enumeration
- Hypothesis-driven testing
- Elimination of non-viable exploit paths
- Successful exploitation via capability abuse

The walkthrough intentionally omits noise and focuses on decision points.

---

## Analysis and Mitigation
The `analysis/` directory contains:
- A detailed assessment of failed SUID exploit paths
- Root cause analysis explaining system behavior
- Mitigation and hardening recommendations to prevent recurrence

These files complete the full security lifecycle:  
**exploit → analysis → remediation → validation**

---
## Disclaimer
This project was created for **educational and research purposes only**.  
All vulnerabilities were intentionally introduced in a controlled lab environment.

No production systems were targeted or harmed.

---

## Key Takeaway
Modern Linux systems are resilient against many legacy privilege escalation techniques.  
However, **misconfigured privilege delegation mechanisms**, such as Linux capabilities, can silently reintroduce critical risk when applied without strict constraints.

Understanding *why* an exploit fails is often as important as knowing how one succeeds.
