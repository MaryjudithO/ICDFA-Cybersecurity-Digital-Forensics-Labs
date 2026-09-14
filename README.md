================================================================================
                    ICDFA LAB DOCUMENTATION (LABS 01, 02, 03)
================================================================================
Student Name: Maryjudith Chidinma Ogunaka
Registration Number: C11/26/FCDF/17151
Programme: ICDFA Trainee | Cohort 11
Target Environments: Metasploitable 2, DVWA, OWASP Mutillidae

================================================================================
LAB 01: NETWORK & SERVICE RECONNAISSANCE
================================================================================

1. EXECUTIVE SUMMARY
-------------------
This lab focused on performing passive and active network reconnaissance against 
an authorized Metasploitable 2 instance. The primary goal was to map open ports, 
identify running services and their versions, detect the host operating system, 
execute Nmap Scripting Engine (NSE) scripts, and conduct web technology 
fingerprinting using WhatWeb.

2. TARGET ENVIRONMENT & SETUP
-----------------------------
- Target System: Metasploitable 2 (Linux VM)
- Primary IP Address (Steps 1–9): 10.15.203.233
- Updated IP Address (Steps 10–30): 10.15.203.91 (DHCP reassignment post-reboot)
- Attacking Platform: Kali Linux

3. TOOLS USED
-------------
- ifconfig / ip addr: Network interface verification
- nmap: Host discovery, port scanning, OS detection, service versioning
- whatweb: Web technology stack fingerprinting
- curl: Direct HTTP header validation

4. DISCOVERED SERVICES INVENTORY
--------------------------------
Port 21   | TCP     | Open | FTP          | vsFTPd 2.3.4
Port 22   | TCP     | Open | SSH          | OpenSSH 4.7p1 Debian
Port 23   | TCP     | Open | Telnet       | Linux telnetd
Port 25   | TCP     | Open | SMTP         | Postfix smtpd
Port 53   | TCP/UDP | Open | DNS          | ISC BIND 9.4.2
Port 80   | TCP     | Open | HTTP         | Apache 2.2.8 (Ubuntu), PHP 5.2.4, DAV/2
Port 111  | TCP/UDP | Open | RPCBind      | RPC #100000
Port 137  | UDP     | Open | NetBIOS-NS   | NetBIOS Name Service
Port 139  | TCP     | Open | NetBIOS-SSN  | Samba smbd 3.X–4.X
Port 445  | TCP     | Open | SMB          | Samba smbd 3.0.20-Debian (SMBv1 enabled)
Port 512  | TCP     | Open | Exec         | netkit-rsh rexecd
Port 513  | TCP     | Open | Login        | OpenBSD / Solaris rlogind
Port 2049 | TCP/UDP | Open | NFS          | Network File System (RPC #100003)
Port 3306 | TCP     | Open | MySQL        | MySQL 5.0.51a-3ubuntu5
Port 5900 | TCP     | Open | VNC          | VNC Protocol 3.3
Port 6667 | TCP     | Open | IRC          | UnrealIRCd 3.2.8.1
Port 8009 | TCP     | Open | AJP13        | Apache JServ Protocol v1.3
Port 8180 | TCP     | Open | HTTP         | Apache Tomcat/Coyote JSP Engine 1.1 (Tomcat 5.5)
Port 8787 | TCP     | Open | DRb          | Ruby Distributed Ruby (DRb)

5. KEY RECONNAISSANCE FINDINGS
------------------------------
- Outdated & Backdoored Software: Target hosts vsFTPd 2.3.4 (CVE-2011-2523) and 
  UnrealIRCd 3.2.8.1.
- Cleartext Authentication: Telnet (23), rexec (512), and rlogin (513) expose 
  credentials in cleartext.
- Legacy Protocols: Samba smbd 3.0.20 supports SMBv1.
- WhatWeb Results: Identified Apache 2.2.8, Ubuntu Linux, PHP 5.2.4, and title 
  "Metasploitable2 - Linux".

6. DEFENSIVE RECOMMENDATIONS
----------------------------
- Upgrade vsFTPd 2.3.4 to a secure version.
- Disable legacy cleartext protocols (Telnet, rexec, rlogin) and enforce SSH.
- Restrict SMB to SMBv2/SMBv3 and disable SMBv1.
- Patch Linux kernel, Apache HTTP server, and PHP runtime.


================================================================================
LAB 02: WEB APPLICATION SECURITY TESTING
================================================================================

1. EXECUTIVE SUMMARY
-------------------
This lab evaluated security controls and common vulnerabilities in web 
applications deployed on Metasploitable 2 (DVWA and OWASP Mutillidae). Scope 
included traffic inspection, session handling, file upload mechanics, 
directory discovery, and SQL injection analysis.

2. TARGET DETAILS
-----------------
- DVWA Endpoint: http://10.234.230.91/dvwa/
- OWASP Mutillidae Endpoint: http://10.234.230.91/mutillidae/
- Tools: Firefox DevTools, Burp Suite, Nikto, DIRB, SQLMap

3. ASSESSMENT MODULES & FINDINGS
--------------------------------
- Traffic & Session Analysis: Captured HTTP traffic via Burp Suite. Isolated 
  cookies: PHPSESSID (session management) and security (DVWA security level).
- File Upload Controls (DVWA Low): Successfully uploaded .txt files and bypassed 
  validation using altered extensions (.log). Demonstrated reliance on client-side 
  or easily tampered MIME headers.
- Nikto Web Scanner: Flagged outdated server components (Apache 2.2.8, PHP 5.2.4), 
  active HTTP TRACE verb, exposed paths (/icons/, /doc/), and missing HTTP 
  security headers.
- DIRB Directory Discovery: Discovered 43 accessible paths, including /dav/, 
  /test/, and administrative interface /phpMyAdmin/.
- SQL Injection (Mutillidae): Single quote (') broke backend queries and revealed 
  raw MySQL syntax errors. Logic testing confirmed boolean payload differences.

4. DEFENSIVE RECOMMENDATIONS
----------------------------
- Enforce strict server-side file upload allowlists and store uploads outside 
  the web root without execution permissions.
- Disable HTTP TRACE in httpd.conf and implement modern HTTP security headers.
- Use Parameterized Queries (Prepared Statements) for database operations and 
  suppress verbose database error messages.


================================================================================
LAB 03: BASH SCRIPTING - AUTOMATED RECONNAISSANCE TOOL
================================================================================

1. OVERVIEW & OBJECTIVES
------------------------
Lab 03 focused on developing recon_tool.sh, an interactive Bash script designed 
to automate basic reconnaissance tasks (WhatWeb, Nmap, DIRB) using custom target 
input and dependency validation.

2. SCRIPT CODE (recon_tool.sh)
------------------------------
#!/usr/bin/env bash
# ==============================================================================
# Script Name: recon_tool.sh
# Description: Interactive automated reconnaissance script for lab targets.
# Author: Maryjudith Chidinma Ogunaka
# Registration: C11/26/FCDF/17151
# Cohort: ICDFA Trainee | Cohort 11
# ==============================================================================

set -euo pipefail

check_dependency() {
    local tool="$1"
    if ! command -v "$tool" &> /dev/null; then
        echo "[!] Error: Required tool '$tool' is not installed or not in PATH."
        return 1
    fi
}

echo "=========================================="
echo "   ICDFA Beginner Reconnaissance Tool"
echo "=========================================="
echo "Use only against authorized lab targets!"
echo ""

read -rp "Enter authorised target IP address or domain: " target

if [ -z "$target" ]; then
    echo "[!] Error: No target provided. Exiting."
    exit 1
fi

echo "[+] Target set to: $target"
echo ""

while true; do
    echo "------------------------------------------"
    echo "Select a reconnaissance tool to execute:"
    echo "1) WhatWeb (Web Tech Fingerprinting)"
    echo "2) Nmap (Service Version Detection)"
    echo "3) DIRB (Web Directory Brute-forcing)"
    echo "4) Exit"
    echo "------------------------------------------"
    read -rp "Enter your choice [1-4]: " choice

    case "$choice" in
        1)
            check_dependency "whatweb" || continue
            echo "[+] Running WhatWeb against http://$target ..."
            whatweb "http://$target"
            ;;
        2)
            check_dependency "nmap" || continue
            echo "[+] Running Nmap service detection (-sV) against $target ..."
            nmap -sV "$target"
            ;;
        3)
            check_dependency "dirb" || continue
            echo "[+] Running DIRB directory discovery against http://$target ..."
            dirb "http://$target"
            ;;
        4)
            echo "[+] Exiting reconnaissance tool. Goodbye!"
            exit 0
            ;;
        *)
            echo "[!] Invalid selection. Please choose an option between 1 and 4."
            ;;
    esac
    echo ""
done

3. EXECUTION VERIFICATION
-------------------------
- Option 1 (WhatWeb): Fingerprinted target stack (Apache 2.2.8, PHP 5.2.4).
- Option 2 (Nmap): Enumerated versions across open ports (FTP, SSH, Telnet, MySQL).
- Option 3 (DIRB): Indexed server pathways (/dav/, /test/, /phpMyAdmin/).
- Option 4 (Exit): Successfully terminated script loop.
