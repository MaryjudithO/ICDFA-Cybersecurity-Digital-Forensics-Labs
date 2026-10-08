# Week 9: Security Baseline Audit and the CIA Triad

**Cisco Networking Academy: Introduction to Cybersecurity** | Module 1: Introduction to Cybersecurity

## Aim

Perform a basic security baseline audit of a Linux host and demonstrate the three CIA triad properties (Confidentiality, Integrity, Availability) with hands-on checks.

## Learning Objectives

- Explain why cybersecurity matters for individuals and organisations.
- Identify what a host exposes (users, services, open ports, patch status).
- Demonstrate integrity checking with cryptographic hashes.
- Demonstrate confidentiality with file permissions.
- Check availability indicators for a host.

## Requirements

- Ubuntu 22.04/24.04 VM (or any Debian-based Linux) with sudo access
- Terminal access
- Internet access for package updates (optional)

> **Safety:** Run these labs only on a virtual machine you own. Take a VM snapshot before you begin.

## Lab Steps

### Step 1: Record system identity

Document the host being assessed. Every audit begins by recording what is being audited.

```bash
hostnamectl
whoami && id
uname -a
```

**Expected result:** Hostname, OS version and kernel are displayed; your user and group IDs are shown.

📸 **Screenshot:** save as `screenshots/01_system_info.png`

### Step 2: Enumerate listening network services

Each listening port is an entry point. Identify which services accept connections.

```bash
sudo ss -tulpn
```

**Expected result:** A table of TCP/UDP listeners with the owning process. A fresh VM usually shows only DNS resolver and, if installed, SSH.

📸 **Screenshot:** save as `screenshots/02_listening_services.png`

### Step 3: Check patch status

Unpatched software is a leading cause of compromise. Check for pending updates.

```bash
sudo apt update
apt list --upgradable 2>/dev/null | head -n 15
```

**Expected result:** A list of upgradable packages, or an up-to-date message.

📸 **Screenshot:** save as `screenshots/03_update_status.png`

### Step 4: Review user accounts and recent logins

Unknown or unused accounts are a risk. List human accounts (UID >= 1000) and recent logins.

```bash
awk -F: '$3>=1000 {print $1, $3, $7}' /etc/passwd
last -n 5
```

**Expected result:** Only expected accounts are listed; recent logins match your own activity.

📸 **Screenshot:** save as `screenshots/04_user_accounts.png`

### Step 5: Demonstrate Integrity with SHA-256

A hash is a fingerprint. If the file changes by even one character, the hash changes.

```bash
mkdir -p ~/lab9 && cd ~/lab9
echo 'Quarterly report: 100 units' > report.txt
sha256sum report.txt | tee report.sha256
echo 'Quarterly report: 900 units' > report.txt
sha256sum -c report.sha256
```

**Expected result:** The check reports `report.txt: FAILED`, proving the tampered file no longer matches the original fingerprint.

📸 **Screenshot:** save as `screenshots/05_integrity_hash.png`

### Step 6: Demonstrate Confidentiality with file permissions

Permissions restrict who can read data. Compare an open file with a protected one.

```bash
cd ~/lab9
chmod 644 report.txt && ls -l report.txt
chmod 600 report.txt && ls -l report.txt
```

**Expected result:** Permissions change from `-rw-r--r--` (others can read) to `-rw-------` (owner only).

📸 **Screenshot:** save as `screenshots/06_confidentiality_perms.png`

### Step 7: Check Availability indicators

Availability means systems and data are usable when needed. Check uptime, disk space and a core service.

```bash
uptime
df -h /
systemctl is-active ssh || systemctl is-active systemd-resolved
```

**Expected result:** Uptime and load are shown, disk has free space, and the service state reports `active`.

📸 **Screenshot:** save as `screenshots/07_availability.png`

## Clean-up

```bash
rm -rf ~/lab9
```

## Deliverables

- Completed screenshots in `screenshots/`
- Completed [REPORT.md](REPORT.md) (fill in the **Student observation** lines)

---
*Author: Maryjudith Chidinma Ogunaka*

