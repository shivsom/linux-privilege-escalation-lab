
# Linux Privilege Escalation Lab

## Overview
This repository contains a controlled Linux privilege escalation lab designed to analyze and demonstrate common post-compromise escalation vectors. The focus is on **reasoned analysis**, not blind exploitation.

The lab highlights:
- Proper and improper use of SUID binaries
- Enumeration and validation of Linux capabilities
- A real-world capability misconfiguration leading to root compromise
- The importance of ruling out false positives during security assessments

---

## Objectives
- Practice systematic privilege escalation enumeration
- Analyze SUID binaries and distinguish secure vs insecure implementations
- Understand Linux capabilities and their security impact
- Demonstrate capability-based privilege escalation
- Document mitigation strategies and defensive lessons

---

## Environment
- **Attacker OS:** Kali Linux
- **Target OS:** Ubuntu Server 24.04 (minimal installation)
- **Access Method:** SSH
- **Initial User:** `dev`

---

## Initial Access
The attacker gains SSH access as a low-privileged user.

```bash
whoami
id
````

Result:

```
uid=1001(dev) gid=1001(dev)
```

---

## Enumeration

### SUID Enumeration

```bash
find / -perm -4000 -type f 2>/dev/null
```

Relevant custom binary identified:

```
/usr/local/bin/vuln
```

---

### Capability Enumeration

```bash
getcap -r / 2>/dev/null
```

Relevant output:

```
/usr/local/bin/capbash cap_setuid=ep
```

---

## SUID Binary Analysis: `vuln`

### Description

`vuln` is a custom SUID binary owned by user `vic`.

### Tests Performed

The following common SUID exploit classes were evaluated:

* PATH hijacking
* Argument injection
* Command chaining
* Unsafe file handling
* Privilege retention

### Result

The binary was determined to be **non-exploitable** due to:

* Use of absolute paths
* No user-controlled input
* No shell invocation with retained privileges

This demonstrates that **SUID binaries are not inherently insecure** when implemented correctly.

---

## Capability Misconfiguration: `capbash`

### Description

The binary `capbash` was assigned the following Linux capability:

```
cap_setuid=ep
```

This capability allows a process to change its UID arbitrarily, including setting UID to root.

---

### Exploitation

When executed, the binary invokes `setuid(0)` before spawning a shell:

```bash
/usr/local/bin/capbash
```

---

### Verification

```bash
whoami
id
```

Result:

```
uid=0(root)
```

This results in full root compromise **without relying on SUID or sudo**.

---

## Impact

* Complete privilege escalation to root
* Bypass of traditional SUID and sudo auditing
* Demonstrates why misconfigured Linux capabilities are high-risk

---

## Mitigation

Recommended defensive measures:

* Regular auditing of file capabilities:

  ```bash
  getcap -r / 2>/dev/null
  ```
* Removal of unnecessary capabilities:

  ```bash
  setcap -r /usr/local/bin/capbash
  ```
* Avoid shell-spawning binaries with elevated privileges
* Enforce least-privilege principles
* Use MAC frameworks such as AppArmor or SELinux

---

## Key Takeaways

* Not all SUID binaries are exploitable
* Capabilities can be more dangerous than SUID when misused
* Enumeration must include both SUID and capabilities
* Eliminating false positives is a critical security skill

---

## Disclaimer

This lab is intended for educational and defensive security research purposes only.

```
