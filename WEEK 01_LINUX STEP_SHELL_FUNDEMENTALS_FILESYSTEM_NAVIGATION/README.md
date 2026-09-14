## 👤 Author
**Maryjudith Chidinma Ogunaka**  
International Cybersecurity and Digital Forensics Academy

---

# ICDFA Digital Forensics Labs

This repository contains hands‑on laboratory work completed as part of the  
**International Cybersecurity and Digital Forensics Academy (ICDFA)** program.

The labs focus on practical Linux command-line essentials, system navigation, file management, permissions, text processing, shell scripting, storage architecture, network analysis, user administration, and security controls essential for digital forensics and cybersecurity operations.

This repository is structured to reflect how real digital forensics lab work and system administration tasks are documented, preserved, and reviewed.

---

## 📘 Repository Contents

### Linux Essentials

### Week 01 – Linux CLI Fundamentals and System Navigation
This lab introduces essential Linux terminal concepts, shell interaction, directory navigation, built-in help features, and workspace hygiene techniques required for operating within Unix-like environments.

**Topics covered:**
- Environment setup (VMware Workstation & Kali Linux VM initialization)
- Shell interface identification and command line orientation (`whoami`, `id`, `pwd`, `echo $SHELL`, `date`)
- Command execution status and exit code validation (`echo $?`)
- Command history capture and output redirection (`history | tail -n 25 > file.txt`)
- Built-in help utilities, manuals, and command discovery (`--help`, `help`, `man`, `apropos`, `whatis`, `command -v`)
- Absolute vs. relative path navigation (`cd`, `pwd`, `mkdir -p`, `find`)
- Workspace creation and safe file handling/deletion (`touch`, `ls -lah`, `file`, `rm -i`)

📄 **Full Step‑by‑Step Documentation:**  
[Documentation.md](./Week01_Linux_Fundamentals/Week01_LinuxFundamentals-Solution-Maryjudith-Documentation.md)

📑 **Formal Lab Report (with screenshots):**  
WADF-2026-M01-WEEK01-C1126FCDF17151-EVIDENCE PDF.pdf

---

### Week 02 – File Management, Archiving, Compression, and Text Processing
This lab focuses on controlled project workspace setup, advanced file lifecycle management, archive creation and checksum integrity validation, and log parsing via stream utilities.

**Topics covered:**
- Controlled directory tree construction (`mkdir -p`, `ls -R`)
- Safe file operations, content modification, and diff analysis (`touch`, `cp`, `mv`, `diff -u`, interactive deletion with `rm -i`)
- File discovery and search criteria based on patterns, sizes, and timestamps (`wildcards *`, `find -size`, `find -mtime`)
- Uncompressed TAR archive creation, content inspection, and extraction (`tar -cvf`, `tar -tf`, `tar -xvf`)
- Gzip stream compression and SHA-256 evidence integrity hashing (`tar -czvf`, `sha256sum`)
- Text inspection, line counting, pattern searching, and data pipelines (`cat`, `head`, `tail`, `wc -l`, `grep`, `cut`, `sort`, `uniq -c`)

📄 **Full Step‑by‑Step Documentation:**  
[Documentation.md](./Week02_File_Management/Week02_FileManagement-Solution-Maryjudith-Documentation.md)

📑 **Formal Lab Report (with screenshots):**  
WADF-2026-M01-WEEK02-C1126FCDF17151-EVIDENCE.pdf

---

### Week 03 – Bash Scripting, Hardware Awareness, and Linux Storage Architecture
This lab emphasizes Bash script automation, interactive user input handling, read-only hardware/system inventory collection, and Linux filesystem/storage mapping.

**Topics covered:**
- First Bash script creation, variable assignment, execution permissions, and logging (`#!/usr/bin/env bash`, `chmod u+x`, `./script.sh | tee`)
- Script logic control: user input, conditional checks, validation, and loops (`read -r -p`, `if [[...]]`, `for...in`, exit codes)
- System hardware, memory, CPU, and virtualization profiling (`lscpu`, `free -h`, `lsblk`, `df -hT`, `systemd-detect-virt`)
- Storage architecture analysis and filesystem mapping (`lsblk -f`, `findmnt`, `/etc/fstab`, `blkid`)
- Core system directory functions and metadata inspection (`stat`, `/home`, `/etc`, `/var`, `/tmp`, `/proc`, `/sys`)
- Space consumption analysis, large file tracking, and cleanup recommendations (`df -h`, `du -h --max-depth=1`, `find -printf`)

📄 **Full Step‑by‑Step Documentation:**  
[Documentation.md](./Week03_Scripting_Storage/Week03_ScriptingStorage-Solution-Maryjudith-Documentation.md)

📑 **Formal Lab Report (with screenshots):**  
WADF-2026-M01-WEEK03-C1126FCDF17151-EVIDENCE PDF.pdf

---

### Week 04 – Network Inspection, Linux Security Posture, User Administration, and Capstone Challenges
This lab covers network interface/route examination, service monitoring, Linux security auditing, multi-department user account management, special file permissions, and automated log analysis.

**Topics covered:**
- Network interface profiling, IP addressing, and routing table analysis (`ip -br link`, `ip -br addr`, `ip route`, `getent hosts`)
- Resolver configuration, DNS lookups, and active socket/port inspection (`/etc/resolv.conf`, `getent ahosts`, `ss -tulpn`, `ss -ltnp`)
- Security posture review: sudo privileges, group memberships, SSH config, firewall status, and patch availability (`sudo -l`, `id`, `getent group sudo`, `grep SSH`, `apt list --upgradable`)
- User and group administration, directory ownership, and permission hardening (`groupadd`, `useradd`, `passwd`, `chown`, `chmod`)
- Special Linux permissions for collaborative environments (`setgid g+s`, `sticky bit 3770`, file permissions `640`)
- Parameterized Bash onboarding script development with pre-flight check validation (`EUID`, error exit codes `1`, `2`, `3`)
- Log file archiving/restoration (`tar -cvf`, `tar -tf`, `tar -xvf -C`) and regex authentication log extraction (`grep -E`, `awk`, `cut`, standard error redirection `2>`)

📄 **Full Step‑by‑Step Documentation:**  
[Documentation.md](./Week04_Network_Security_Admin/Week04_NetworkSecurityAdmin-Solution-Maryjudith-Documentation.md)

📑 **Formal Lab Report (with screenshots):**  
WADF-2026-M01-WEEK04-C1126FCDF17151-EVIDENCE REPORT (1).pdf

---

## 🧪 Tools and Environment
- VMware Workstation / Oracle VirtualBox
- Kali Linux VM (Hostname: ICDFA / MARYJUDITH)
- Bash Shell & Zsh CLI (`/usr/bin/zsh`)
- Core Linux Utilities (`grep`, `awk`, `sed`, `tar`, `sha256sum`, `find`, `diff`)
- System Administration & Network Utilities (`ip`, `ss`, `lscpu`, `lsblk`, `systemd-detect-virt`)

---

## 🎯 Learning Outcomes
- Master standard Linux CLI navigation, command history management, and built-in help tools
- Perform structured file lifecycle operations, differential file checking, and safe file management
- Construct, compress, verify, and restore TAR/Gzip archives using cryptographic SHA-256 hashes
- Write robust, parameterized Bash scripts featuring conditional input validation and automated directory/user creation
- Map storage layouts, inspect filesystem mount points, and profile system hardware/virtualization environments safely
- Analyze network configurations, default gateway routes, DNS resolution, and listening network ports
- Audit security postures, implement multi-department access controls, and manage special Linux permissions (`setgid`, `sticky bit`)
- Parse security log files using command pipelines, regular expressions, and stream redirection for forensic investigations

---

## 📌 Disclaimer
This repository is intended for **educational and academic purposes only**.
All labs were performed in controlled environments using test data and virtual machines.

---
