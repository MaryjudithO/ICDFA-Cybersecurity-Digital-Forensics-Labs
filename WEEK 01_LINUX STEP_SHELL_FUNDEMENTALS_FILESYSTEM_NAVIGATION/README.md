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

### Week 05 – Shell Configuration, File Globbing, and Advanced File Manipulation
This week focuses on shell environment customization, shell history handling, alias management, file globbing using wildcards, and advanced file operations such as recursive directory copying, renaming, and interactive file deletion.

**Topics covered:**
- Shell environment discovery and configuration inspection (`echo $SHELL`, `pwd`, `cat ~/.bashrc`, `env`) 
- Command history filtering and output limiting (`history | tail`) 
- Shell customization, path exported variables, and command aliasing (`alias ll='ls -l'`, `alias`) 
- File globbing and wildcard operations (`*`, `?`, `[]`, `{}`) 
- File content redirection (`>`, `>>`, `cat`) 
- Advanced file manipulation, copying, moving, and interactive deletion (`touch`, `cp`, `cp -r`, `mv`, `rm`, `rm -i`, `rm -r`) 

📄 **Full Step‑by‑Step Documentation:**  
[Documentation.md](./Week05_Shell_Globbing_Manipulation/Week05_ShellGlobbingManipulation-Solution-Maryjudith-Documentation.md)

📑 **Formal Lab Report (with screenshots):**  
WADF-2026-M02-WEEK05 -C1126FCDF17151- EVIDENCE PDF.pdf 

---

### Week 06 – Finding Files, Text Utilities, Regular Expressions, and Vi Editor
This week covers advanced file searching, stream-based text manipulation and data sorting, regular expression pattern matching, and modal text editing with the `vi`/`vim` editor.

**Topics covered:**
- Deep filesystem searches by name, case sensitivity, file type, modification time, and database updating (`find -type f`, `find -iname`, `find -mtime`, `locate`, `sudo updatedb`) 
- Structured text processing, field extraction, sorting, line counting, and stream translation (`cut -d -f`, `sort -t -k -n`, `uniq -c`, `tr`, `wc -l`) 
- Pattern matching using standard and extended regular expressions (`grep`, `grep -E`, `grep -i`, character classes, anchors `^` and `$`) [cite: 10]
- Text editing using the Vi editor: command/insert modes, navigation, text insertion, line deletion, and file saving (`vi`, `-- INSERT --`, `:w`, `:wq`) 

📄 **Full Step‑by‑Step Documentation:**  
[Documentation.md](./Week06_Find_Text_Regex_Vi/Week06_FindTextRegexVi-Solution-Maryjudith-Documentation.md)

📑 **Formal Lab Report (with screenshots):**  
WADF-2026-M02-WEEK06 -C1126FCDF17151- EVIDENCE pdf.pdf 

---

### Week 07 – Standard Text Streams, Processes, Archives, and File Permissions
This week emphasizes standard I/O streams and redirection operators, system process management and background job execution, TAR archive construction/extraction, and Linux permission/ownership administration.

**Topics covered:**
- Standard input, output, standard error redirection, piping, and stream splitting (`>`, `>>`, `<`, `2>`, `2>&1`, `|`, `tee`) 
- Process monitoring, background execution, job control, signals, and process tree visualization (`ps aux`, `top`, `&`, `jobs`, `fg`, `bg`, `kill %1`, `nice`, `pstree`) 
- File archiving, listing, extraction, and compressed archive management (`tar -cvf`, `ls -lh`, `tar -tvf`, `tar -xvf`, `rm`) [cite: 11]
- Permission administration, numeric octal modes, recursive ownership assignment, umask configuration, and special permissions (`chmod`, `chmod -R`, `chown`, `chgrp`, `umask`, `sticky bit +t`) 

📄 **Full Step‑by‑Step Documentation:**  
[Documentation.md](./Week07_Streams_Processes_Permissions/Week07_StreamsProcessesPermissions-Solution-Maryjudith-Documentation.md)

📑 **Formal Lab Report (with screenshots):**  
WADF-2026-M02-WEEK07-C1126FCDF17151-Linux_Evidence_Report_Maryjudith.pdf 

---

### Week 08 – Month 2 Comprehensive Assessment & Advanced Linux Administration
This week combines filesystem link behavior analysis, system hardware profiling, boot sequence examination, filesystem recovery, package management, archive compression validation, and practical assessment scenario execution.

**Topics covered:**
- Hard links vs. symbolic links creation, inode validation, and broken link behavior (`ln`, `ln -s`, `ls -i`, `ls -il`, `cat`, `rm`) 
- System administration concepts: bootloader mechanics, runlevel targets, filesystem integrity checks, shared library dependencies, and package management 
- End-to-end multi-tier project directory setup, file cleanup, and directory tree validation (`mkdir -p`, `touch`, `rm`, `ls -R`, `find`) 
- Structured text parsing and employee record salary sorting (`cut`, `sort -t ',' -k3 -n`, `grep`) 
- Background job prioritization and process management (`sleep &`, `jobs`, `ps`, `top`, `nice`, `kill`, `pstree`) 
- Restricted directory security configuration and sticky bit application (`mkdir`, `chmod 700`, `chmod 644`, `chmod +t`) 
- Archive creation, dual compression comparison, integrity validation, and extraction (`tar -cvf`, `gzip`, `bzip2`, `tar -tvf`, `tar -xzf`) 
- Month 2 Multiple-Choice Questions (MCQ) assessment and holistic reflection 

📄 **Full Step‑by‑Step Documentation:**  
[Documentation.md](./Week08_Month2_Assessment/Week08_Month2Assessment-Solution-Maryjudith-Documentation.md)

📑 **Formal Lab Report (with screenshots):**  
WADF-2026-WEEK08-Month2_Assessment-C11-26-FCDF-17151.docx.pdf

---

## 🧪 Tools and Environment
- VMware Workstation / Oracle VirtualBox 
- Kali Linux VM (Hostname: ICDFA / MARYJUDITH) 
- Bash Shell & Zsh CLI (`/usr/bin/zsh`) 
- Core Linux Utilities (`grep`, `awk`, `sed`, `tar`, `sha256sum`, `find`, `diff`, `cut`, `sort`, `uniq`, `tr`, `wc`) 
- System Administration & Network Utilities (`ip`, `ss`, `lscpu`, `lsblk`, `systemd-detect-virt`, `ps`, `top`, `pstree`, `kill`, `chmod`, `chown`, `chgrp`) 
- Text Editors (`vi`, `nano`) 

---

## 🎯 Learning Outcomes
- Master standard Linux CLI navigation, command history management, and built-in help tools 
- Customize the shell environment, manage configuration files (`.bashrc`), create command aliases, and utilize file globbing patterns 
- Perform structured file lifecycle operations, differential file checking, recursive directory copying, renaming, and safe file management 
- Search files by name, type, size, modification time, or permissions, and query system file databases 
- Inspect, clean, sort, and transform text streams using regular expressions, pipeline commands, and stream editors 
- Create, edit, save, and manage files using modal Vi/Vim and Nano text editors 
- Manage standard streams (`stdin`, `stdout`, `stderr`), output redirection, error handling, and pipeline splitting 
- Control system processes, background job execution, process prioritization, signals, and process tree structures 
- Construct, compress (`gzip`, `bzip2`), verify, and restore TAR archives using cryptographic hashes and listing tools 
- Audit security postures, configure permissions using octal/symbolic modes, assign recursive group/user ownership, and set special bits (`setgid`, `sticky bit`) 
- Understand filesystem link behaviors (hard links vs. symbolic links), boot sequence mechanics, runlevel targets, package managers, and system recovery procedures 

---

## 📌 Disclaimer
This repository is intended for **educational and academic purposes only**.
All labs were performed in controlled environments using test data and virtual machines .

---
