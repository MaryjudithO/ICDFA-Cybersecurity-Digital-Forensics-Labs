# Lab 1: Network & Service Reconnaissance with Metasploitable 2

## 📋 Overview
This repository documents a hands-on network reconnaissance and service enumeration exercise performed against a **Metasploitable 2** virtual machine in an isolated, authorized lab environment. The assessment uses **Nmap** and **WhatWeb** to identify active hosts, enumerate open ports and services, detect the operating system, and fingerprint web application technologies.

| | |
|---|---|
| **Student** | Maryjudith Chidinma Ogunaka |
| **Registration No.** | C11/26/FCDF/17151 |
| **Programme** | ICDFA Trainee — Cohort 11 |
| **Date** | 05 September 2026 |
| **Target** | Metasploitable 2 Virtual Machine (authorized lab target) |
| **Scanner** | Kali Linux |

---

## 🎯 Objectives
- Identify the IP addresses of the Kali Linux scanner and the Metasploitable 2 target, and verify connectivity
- Perform host discovery to confirm the target was active and reachable
- Run default, service version, OS detection, aggressive, full TCP, and UDP scans with Nmap
- Use the Nmap Scripting Engine (NSE) to enumerate HTTP, SMB, SSH, and FTP services
- Fingerprint the target's web application using WhatWeb at multiple aggression levels
- Build a comprehensive service inventory and document all findings

---

## 🛠️ Tools Used
- **Nmap 7.95** — host discovery, port scanning, service/version detection, OS fingerprinting, NSE scripting
- **WhatWeb 0.6.4-1** — web application and technology fingerprinting
- **curl** — manual HTTP header verification

---

## 🗂️ Methodology Summary

| Step | Task | Command |
|---|---|---|
| 1–3 | Identify scanner/target IPs, verify connectivity | `ifconfig`, `ip addr`, `ping` |
| 4 | Host discovery | `nmap -sn 10.15.203.0/24` |
| 5 | Default TCP scan | `nmap <target>` |
| 6–7 | Service version detection | `nmap -sV`, `nmap -sV --version-intensity 9` |
| 8 | OS detection | `sudo nmap -O` |
| 9 | Aggressive scan | `sudo nmap -A` |
| 10–14 | Full port scans, timing, ranges | `nmap -p- `, `-T4`, `-p 1-1024`, selected ports |
| 15–16 | UDP scanning | `sudo nmap -sU`, `--top-ports 20 -sV` |
| 17 | Default NSE script scan | `nmap -sC -sV` |
| 18–22 | Targeted NSE enumeration | `http-title`, `http-headers`, `smb-protocols`, `ssh-hostkey`, `banner` |
| 23 | Manual HTTP verification | `curl -I` |
| 24–30 | Web fingerprinting | `whatweb` (basic, verbose, aggression levels 1–4, redirects, saved output) |

> ⚠️ **Note:** The target's IP address changed mid-assessment (`10.15.203.233` → `10.15.203.91`) after a VM restart. This was verified via `ifconfig` and all subsequent steps (10–30) were redirected to the updated address. Results collected before the change remain valid as both addresses belonged to the same host.

---

## 🔍 Key Findings

**Open TCP Services:** FTP, SSH, Telnet, SMTP, DNS, HTTP, RPCBind, SMB, NetBIOS, NFS, MySQL, VNC, IRC, Apache Tomcat, Java RMI

**Open UDP Services:** DNS, NetBIOS Name Service, RPCBind, NFS

**Web Stack (via WhatWeb / NSE):**
- Apache 2.2.8 (Ubuntu) with WebDAV v2
- PHP 5.2.4-2ubuntu5.10
- Page title: `Metasploitable2 - Linux`

**Operating System:** Linux 2.6.9 – 2.6.33 (general purpose)

---

## 🚨 Security Observations

| Finding | Risk |
|---|---|
| vsFTPd 2.3.4 (port 21) | Historically associated with a known backdoor (CVE-2011-2523) |
| Telnet enabled (port 23) | Transmits credentials in plaintext |
| SMBv1 enabled (port 445) | Legacy protocol with well-documented vulnerabilities |
| Apache 2.2.8 / PHP 5.2.4 | End-of-life, unpatched software |
| rexec / rlogin (512/513) | Weak, trust-based authentication |
| WebDAV enabled | Expands attack surface if misconfigured |
| Large number of exposed services | Increases overall attack surface |

---

## ✅ Recommendations
- Upgrade or disable vsFTPd 2.3.4
- Disable Telnet, rexec, rlogin, rsh — replace with SSH
- Disable SMBv1; enforce SMBv2/SMBv3
- Upgrade Apache and PHP to supported, patched versions
- Review and restrict non-essential services (MySQL, VNC, IRC, Tomcat, AJP13)
- Apply least-privilege access controls and network segmentation
- Conduct regular vulnerability assessments

---


```
Screenshots are numbered sequentially and labeled by the lab step / task they document, matching the order of the full report.

---

## 📌 Disclaimer
All scanning and enumeration activities were performed exclusively against an authorized, isolated lab target (Metasploitable 2) as part of the ICDFA training programme. No unauthorized systems were accessed or scanned.
