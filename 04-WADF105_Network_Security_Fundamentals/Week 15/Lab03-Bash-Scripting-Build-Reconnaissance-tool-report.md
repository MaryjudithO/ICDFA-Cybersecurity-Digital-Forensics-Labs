# Lab 3: Bash Scripting - Build a Reconnaissance Tool

| | |
|---|---|
| **Student Name** | Maryjudith Chidinma Ogunaka |
| **Registration Number** | C11/26/FCDF/17151 |
| **Programme** | ICDFA Trainee, Cohort 11 |
| **Lab Title** | Lab 3 - Bash Scripting: Build a Reconnaissance Tool |
| **Date** | 05 September 2026 |
| **Authorized Target** | 10.15.203.91 |
| **Testing Machine** | Kali Linux virtual machine (10.15.203.36) |

> **Authorisation note:** every scan in this report was run only against the authorised ICDFA lab target `10.15.203.91`.

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Objective](#objective)
3. [Setting Up the Environment](#setting-up-the-environment)
4. [Building the Script, Stage by Stage](#building-the-script-stage-by-stage)
5. [Saving, Permissions and Running the Script](#saving-permissions-and-running-the-script)
6. [Confirming the Target Is Reachable](#confirming-the-target-is-reachable)
7. [Testing Against the Authorized Target](#testing-against-the-authorized-target)
8. [Script Backup and Verification](#script-backup-and-verification)
9. [Observations From the Scan Results](#observations-from-the-scan-results)
10. [Complete Script](#complete-script)
11. [Challenges Encountered](#challenges-encountered)
12. [What I Learned](#what-i-learned)
13. [Answers to Understanding Questions](#answers-to-understanding-questions)
14. [Conclusion](#conclusion)

---

## Executive Summary

In this lab, I developed a Bash script that automates three common reconnaissance tools, WhatWeb, Nmap and DIRB, in a single menu-driven application. Instead of running each command by hand, the script asks the user to enter an authorised target, checks that the input is not empty, shows a menu of the available tools, and runs the selected tool against the target.

The script was built step by step rather than all at once. I began with the header and banner message, then added user input for the target address, then input validation to block empty submissions, then the menu, and finally a `case` statement that runs the chosen tool. Once it was complete, the script was saved, made executable with `chmod +x`, and run directly with `./recon_tool.sh`.

The finished script was tested against the authorised lab target `10.15.203.91`, and all three menu options worked. WhatWeb identified the web server technologies, Nmap performed service detection and found multiple open ports with their running services, and DIRB discovered directories and files hosted on the target. This lab strengthened my understanding of Bash variables, user input, conditional statements (`if`), `case` statements and functions. It also reinforced that reconnaissance must only be done against systems for which explicit authorisation has been given.

---

## Objective

The objective of this lab was to create a Bash script that:

- Prompts the user to enter a target IP address or domain.
- Ensures the target is not hardcoded within the script.
- Validates that a target has been supplied.
- Displays a menu containing:
  - WhatWeb
  - Nmap
  - DIRB
  - Exit
- Uses a `case` statement to handle menu selections.
- Verifies that the required tools are installed before execution.
- Runs the selected reconnaissance tool against the user-supplied target.
- Is executable and can be run directly from the terminal.

---

## Setting Up the Environment

Before writing the script, I created a dedicated working directory and checked that all the required tools were installed.

```bash
mkdir -p ~/lab3-recon
cd ~/lab3-recon
```

I then checked that each required tool was available:

```bash
command -v bash
command -v nmap
command -v whatweb
command -v dirb
```

The `command -v` command prints the location of a program if it is installed. All four commands returned valid paths, which confirmed the required tools were available.


![Screenshot 01 - Confirming bash, nmap, whatweb and dirb are installed](Lab3-Screenshots/01_setup-verify-tools-installed.png)

**Screenshot 01 - Confirming bash, nmap, whatweb and dirb are installed.** The terminal is in the working folder `~/lab3-recon`. Each `command -v` check prints a path: `/usr/bin/bash`, `/usr/bin/nmap`, `/usr/bin/whatweb` and `/usr/bin/dirb`. Because every command returned a path, nothing needed installing before the script was written. The last line shows the next action, `nano recon_tool.sh`, which opens the editor to start writing the script.


---

## Building the Script, Stage by Stage

Rather than writing the whole script in one go, I built it gradually in `nano` and tested at each stage before moving on.

### Stage A - Header and banner

The script opens with a shebang line and three `echo` statements that print a title, a divider and a reminder that it must only be used against authorised targets. The shebang (`#!/usr/bin/env bash`) tells Linux to run the file with Bash.

### Stage B - Reading the target

`read -rp` prompts the user and stores the answer in the variable `target`. The `-r` option stops backslashes from being treated specially, and `-p` shows the prompt text on the same line. Because the target comes from the user, it is never hardcoded.

### Stage C - Validating the input

An `if [[ -z "$target" ]]` check tests whether the `target` variable is empty. If the user presses Enter without typing anything, the script prints an error and exits with `exit 1`, so it never runs a scan with a blank target.


![Screenshot 02 - Header, target prompt and empty-input validation in nano](Lab3-Screenshots/02_stageA-C-nano-header-prompt-validation.png)

**Screenshot 02 - Header, target prompt and empty-input validation in nano.** This is the top of `recon_tool.sh` in GNU nano 8.3. Line 1 is the shebang `#!/usr/bin/env bash`. The three `echo` lines print the title *ICDFA Beginner Reconnaissance Tool*, a divider line and the warning *Use only against authorized lab targets*, followed by a blank `echo`. The `read -rp "Enter authorised target IP address or domain: " target` line collects the target, and `echo "Target entered: $target"` repeats it back so the user can check it. The `if [[ -z "$target" ]]; then ... exit 1; fi` block at the bottom is the validation from Stage C: it prints *Error: no target was entered.* and stops the script. The footer message *[ Read 50 lines ]* is nano telling us how many lines of the file it loaded.


### Stage D - Building the menu

A short block of `echo` statements lists four options (WhatWeb, Nmap, DIRB and Exit), and `read -rp` stores the user's number choice in a variable called `choice`.

### Stage E and F - The case logic and the tool-check function

A `case "$choice" in ... esac` statement compares `choice` against 1, 2, 3 and 4, with a catch-all `*` branch for anything invalid. Before running any tool, a small `check_tool()` function uses `command -v` to confirm the tool is installed. If it is not, the script prints an error and stops instead of failing halfway through a scan.


![Screenshot 03 - Menu and the start of the case statement](Lab3-Screenshots/03_stageD-E-nano-menu-and-case-start.png)

**Screenshot 03 - Menu and the start of the case statement.** The editor now shows the end of the validation block (`exit 1`, `fi`) followed by the menu. Five `echo` lines print *Select a reconnaissance tool:* and the four options `1) WhatWeb`, `2) Nmap`, `3) DIRB` and `4) Exit`. Then `read -rp "Enter your choice [1-4]: " choice` stores the answer. The `case "$choice" in` line begins the selection logic, and the first branch `1)` already calls `check_tool whatweb`. The `*` in the title bar (`recon_tool.sh *`) means the file has unsaved changes.

![Screenshot 04 - Case branches for WhatWeb, Nmap and DIRB](Lab3-Screenshots/04_stageE-G-nano-case-whatweb-nmap-dirb.png)

**Screenshot 04 - Case branches for WhatWeb, Nmap and DIRB.** The `case` statement now has its three tool branches. **Branch 1:** `check_tool whatweb`, then `echo "[+] Running WhatWeb against $target"`, then `whatweb "http://$target"`, ended with `;;`. **Branch 2:** `check_tool nmap`, an `echo` message, then `nmap -sV "$target"`. **Branch 3:** `check_tool dirb` followed by its `echo` message (the DIRB command itself is on the next screen). Each branch first confirms the tool exists, then prints a `[+]` status line, then runs the tool. WhatWeb and DIRB receive `http://$target` because they work on web URLs, while Nmap receives the bare address. The variable is quoted as `"$target"` to avoid Bash syntax problems.


### Stage G - Putting it all together

The final `case` block ties everything together. Option 1 checks for and runs WhatWeb against `http://$target`. Option 2 checks for and runs `nmap -sV $target`. Option 3 checks for and runs DIRB against `http://$target`. Option 4 exits cleanly without scanning anything. Any other input falls through to the `*` branch and is rejected as an invalid choice.


![Screenshot 05 - Finished case statement with DIRB, Exit and invalid-choice branches](Lab3-Screenshots/05_stageG-nano-case-dirb-exit-default-esac.png)

**Screenshot 05 - Finished case statement with DIRB, Exit and invalid-choice branches.** This screen completes the script. Branch 3 finishes with `dirb "http://$target"` and `;;`. **Branch 4** prints *Exiting. No scan was run.* and ends with `exit 0` (a clean, successful exit, since the user chose to leave). **The `*)` branch** is the catch-all: it prints *Error: invalid menu choice.* and uses `exit 1` to signal an error. The statement is closed with `esac` (`case` written backwards), which marks the end of the block.


---

## Saving, Permissions and Running the Script

After writing the script in nano, I saved it with `Ctrl+O` then `Enter`, and exited with `Ctrl+X`. Next I made the file executable and confirmed the permission had been applied.

```bash
chmod +x recon_tool.sh
ls -l recon_tool.sh
```


![Screenshot 06 - chmod +x and confirming the executable permission](Lab3-Screenshots/06_permissions-chmod-plus-x-and-ls-l.png)

**Screenshot 06 - chmod +x and confirming the executable permission.** The terminal shows `nano recon_tool.sh`, then `chmod +x recon_tool.sh` to add execute permission. The next command, `ls l recon_tool.sh`, was typed **without the dash** and produced the error *ls: cannot access 'l': No such file or directory* (it also listed `recon_tool.sh` because `ls` treated the file name as a valid argument). The command was corrected to `ls -l recon_tool.sh`, which prints `-rwxrwxr-x 1 icdfa icdfa 1030 Sep 5 21:55 recon_tool.sh`. The `x` characters in `-rwxrwxr-x` show the owner and group can now execute the file, and everyone else can read and execute it. The file was **1030 bytes** at this stage, and the name is shown in green, which the terminal uses for executable files.


With execute permission in place, I ran the script directly from the terminal using `./recon_tool.sh`. The `./` tells the shell to run the file from the current folder rather than searching the system `PATH` for it.

---

## Confirming the Target Is Reachable

Before scanning, I confirmed that the lab machine could actually reach the target, first by checking my own network details with `ip addr` and then with a ping test.


![Screenshot 07 - Checking the Kali machine's network details](Lab3-Screenshots/07_network-ip-addr-and-ping-command.png)

**Screenshot 07 - Checking the Kali machine's network details.** `ip addr` lists the network interfaces. The loopback interface `lo` has `127.0.0.1`. The main interface **`eth0` is UP** with the address **`10.15.203.36/24`** (broadcast `10.15.203.255`), marked *dynamic*, meaning it was given by DHCP. This shows the Kali machine is on the same `10.15.203.0/24` network as the target `10.15.203.91`, so they can reach each other directly. The last line is the next command, `ping -c 4 10.15.203.91`.

![Screenshot 08 - Ping test to the target](Lab3-Screenshots/08_network-ping-target-reachable.png)

**Screenshot 08 - Ping test to the target.** `ping -c 4 10.15.203.91` sends four test packets. All four replies came back (`icmp_seq=1` to `4`, `ttl=64`) with times between 0.862 ms and 1.97 ms. The summary reads **4 packets transmitted, 4 received, 0% packet loss**, with min/avg/max round-trip times of 0.862/1.536/1.972 ms. This confirms the target was online and reachable before any scan was launched.


---

## Testing Against the Authorized Target

I tested the script against the authorised lab target `10.15.203.91` and worked through all three menu options one at a time.

### Test 1: Nmap (menu option 2)

Selecting option 2 ran `nmap -sV` against the target. The scan returned a long list of open ports with their detected service versions, including FTP (vsftpd 2.3.4), SSH (OpenSSH 4.7p1), Telnet, SMTP (Postfix), DNS (ISC BIND), HTTP (Apache 2.2.8) and several others. This confirms `-sV` works, because it returns specific version numbers rather than only port states.


![Screenshot 09 - Launching the script and choosing the Nmap option](Lab3-Screenshots/09_test1-nmap-run-script-select-option-2.png)

**Screenshot 09 - Launching the script and choosing the Nmap option.** `./recon_tool.sh` starts the script. The banner prints, the prompt receives `10.15.203.91`, and the script echoes *Target entered: 10.15.203.91*. The menu appears and `2` is entered at *Enter your choice [1-4]*. The script then prints its status line *[+] Running Nmap service detection against 10.15.203.91* and Nmap 7.95 starts at `2026-09-05 22:45 WAT`. This shows the target is typed in at run time (not hardcoded) and that the menu passes it to the right tool.

![Screenshot 10 - Nmap results, first part](Lab3-Screenshots/10_test1-nmap-results-ports-part1.png)

**Screenshot 10 - Nmap results, first part.** Nmap reports the host **is up with 0.00080 s latency**, and **977 closed TCP ports** are not shown. The open ports listed are: `21/tcp ftp` (vsftpd 2.3.4), `22/tcp ssh` (OpenSSH 4.7p1 Debian 8ubuntu1, protocol 2.0), `23/tcp telnet` (Linux telnetd), `25/tcp smtp` (Postfix smtpd), `53/tcp domain` (ISC BIND 9.4.2), `80/tcp http` (Apache httpd 2.2.8, Ubuntu, DAV/2), `111/tcp rpcbind`, `139/tcp` and `445/tcp netbios-ssn` (Samba smbd 3.X - 4.X, workgroup WORKGROUP), `512/tcp exec` (netkit-rsh rexecd), `513/tcp login` (OpenBSD or Solaris rlogind) and `514/tcp tcpwrapped`.

![Screenshot 11 - Nmap results, second part](Lab3-Screenshots/11_test1-nmap-results-ports-part2.png)

**Screenshot 11 - Nmap results, second part.** The list continues with `1099/tcp java-rmi` (GNU Classpath grmiregistry), **`1524/tcp bindshell` (Metasploitable root shell)**, `2049/tcp nfs`, `2121/tcp ftp` (ProFTPD 1.3.1), `3306/tcp mysql` (MySQL 5.0.51a-3ubuntu5), `5432/tcp postgresql` (PostgreSQL 8.3.0 - 8.3.7), `5900/tcp vnc` (protocol 3.3), `6000/tcp X11` (access denied), `6667/tcp irc` (UnrealIRCd), `8009/tcp ajp13` (Apache Jserv 1.3) and `8180/tcp http` (Apache Tomcat/Coyote JSP engine 1.1). The scan also shows the MAC address `00:0C:29:49:C3:1A` (VMware) and the service info *metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux*.

![Screenshot 12 - Nmap scan completed](Lab3-Screenshots/12_test1-nmap-scan-complete.png)

**Screenshot 12 - Nmap scan completed.** The end of the output shows the last ports again, then *Service detection performed* and the final line **Nmap done: 1 IP address (1 host up) scanned in 12.68 seconds**. The prompt returns to `~/lab3-recon`, showing the script finished normally. In total 23 open ports were detected.


### Test 2: WhatWeb (menu option 1)

WhatWeb ran successfully and fingerprinted the web server. It identified an Ubuntu Apache 2.2.8 server running PHP 5.2.4, with the page title *Metasploitable2 - Linux*, which confirms the target is the intentionally vulnerable Metasploitable2 lab machine.


![Screenshot 13 - Launching the script and choosing the WhatWeb option](Lab3-Screenshots/13_test2-whatweb-run-select-option-1.png)

**Screenshot 13 - Launching the script and choosing the WhatWeb option.** The script is run again and `1` is entered at the menu. The status line *[+] Running WhatWeb against 10.15.203.91* appears, and WhatWeb starts printing its result: `http://10.15.203.91 [200 OK] Apache[2.2.8]`, `Country[RESERVED][ZZ]` and `HTTPServer[Ubuntu Linux][Apache/2.2.8 (Ubuntu) DAV/2]`. The output is cut off at the edge of the screen and continues in the next screenshot.

![Screenshot 14 - WhatWeb results](Lab3-Screenshots/14_test2-whatweb-results.png)

**Screenshot 14 - WhatWeb results.** The complete WhatWeb output for the target: **HTTP status 200 OK**, **Apache 2.2.8**, **HTTPServer Ubuntu Linux (Apache/2.2.8 (Ubuntu) DAV/2)**, **IP 10.15.203.91**, **PHP 5.2.4-2ubuntu5.10**, **Title: Metasploitable2 - Linux**, **WebDAV 2** and **X-Powered-By: PHP/5.2.4-2ubuntu5.10**. The country shows `RESERVED [ZZ]` because 10.x.x.x is a private address. It tells us the technologies behind the website without sending any attack traffic, which is the point of fingerprinting.


### Test 3: DIRB (menu option 3)

Option 3 ran `dirb` against `http://10.15.203.91`. DIRB worked through its wordlist and discovered real directories on the server, including a phpMyAdmin folder with several accessible files inside it (calendar, changelog, docs, index.php and more). This is solid evidence that the tool ran correctly against a live target.


![Screenshot 15 - Launching the script and choosing the DIRB option](Lab3-Screenshots/15_test3-dirb-run-select-option-3.png)

**Screenshot 15 - Launching the script and choosing the DIRB option.** The script is run and `3` is entered. The status line *[+] Running DIRB against 10.15.203.91* appears and the DIRB v2.22 banner starts to print.

![Screenshot 16 - DIRB scan start details](Lab3-Screenshots/16_test3-dirb-scan-start-wordlist.png)

**Screenshot 16 - DIRB scan start details.** DIRB shows its settings: **START_TIME Sat Sep 5 22:53:35 2026**, **URL_BASE http://10.15.203.91/**, and the wordlist **/usr/share/dirb/wordlists/common.txt**. It generated **4612 words** to try. The first findings are `/cgi-bin/` (status **403**, size 293), meaning it exists but access is forbidden, and a directory `/dav/`.

![Screenshot 17 - DIRB results: root directories](Lab3-Screenshots/17_test3-dirb-results-root-directories.png)

**Screenshot 17 - DIRB results: root directories.** Directories and files found at the top level: `/index` and `/index.php` (**200**, 891 bytes), `/phpinfo` and `/phpinfo.php` (200, about 48 KB), the directory `/phpMyAdmin/`, `/server-status` (403), `/test/` and `/twiki/`. DIRB warns that **`/dav/` is listable**, meaning its contents can be viewed in a browser, so it skips scanning inside it.

![Screenshot 18 - DIRB results: phpMyAdmin files (part 1)](Lab3-Screenshots/18_test3-dirb-results-phpmyadmin-part1.png)

**Screenshot 18 - DIRB results: phpMyAdmin files (part 1).** DIRB enters `/phpMyAdmin/` and finds `calendar`, `changelog` (74,593 bytes), `ChangeLog` and a `contrib/` directory, plus `docs`, `error`, `export`, `favicon.ico`, `import`, `index` and `index.php`, all with status **200**, followed by a `js/` directory.

![Screenshot 19 - DIRB results: phpMyAdmin files (part 2)](Lab3-Screenshots/19_test3-dirb-results-phpmyadmin-part2.png)

**Screenshot 19 - DIRB results: phpMyAdmin files (part 2).** More phpMyAdmin content: directories `lang/` and `libraries/`, and files `license`, `LICENSE`, `main`, `navigation`, `phpinfo`, `phpinfo.php` (size 0), `phpmyadmin`, `print`, `readme`, `README`, `robots` and `robots.txt` (26 bytes), all **200**. Then the directory `scripts/` is found.

![Screenshot 20 - DIRB results: phpMyAdmin setup, themes and test](Lab3-Screenshots/20_test3-dirb-results-phpmyadmin-part3.png)

**Screenshot 20 - DIRB results: phpMyAdmin setup, themes and test.** DIRB finds `phpMyAdmin/setup/` with `sql` (200), then `test/` and `themes/` with files `TODO` (235 bytes) and `webapp` (6900 bytes). It reports that `/test/` **is listable**.

![Screenshot 21 - DIRB results: TWiki](Lab3-Screenshots/21_test3-dirb-results-twiki.png)

**Screenshot 21 - DIRB results: TWiki.** DIRB enters `/twiki/` and finds `bin/` with `data` (**403**), `index` and `index.html` (200, 782 bytes), `lib/` with `license` (19,440 bytes), and `pub/` with `readme` (200) and `templates` (403). The 403 results exist but are blocked from direct access.

![Screenshot 22 - DIRB listable-directory warnings (part 1)](Lab3-Screenshots/22_test3-dirb-listable-warnings-part1.png)

**Screenshot 22 - DIRB listable-directory warnings (part 1).** The scan continues with the `twiki/pub` files and then warns that `phpMyAdmin/contrib/`, `js/` and `lang/` **are listable**. DIRB skips scanning them because their file lists can already be viewed.

![Screenshot 23 - DIRB results: phpMyAdmin setup (part 1)](Lab3-Screenshots/23_test3-dirb-results-phpmyadmin-setup-part1.png)

**Screenshot 23 - DIRB results: phpMyAdmin setup (part 1).** After a listable warning for `phpMyAdmin/scripts/`, DIRB enters `phpMyAdmin/setup/` and finds `config` (status **303**, a redirect, 1370 bytes), a `frames/` directory, and `index` and `index.php` (200, about 8.6 KB). A reachable `setup` area is a sensitive exposure.

![Screenshot 24 - DIRB results: phpMyAdmin setup (part 2)](Lab3-Screenshots/24_test3-dirb-results-phpmyadmin-setup-part2.png)

**Screenshot 24 - DIRB results: phpMyAdmin setup (part 2).** Inside `setup/lib/` DIRB finds `scripts` (21,967 bytes) and `styles` (6218 bytes), both 200. It then reports `phpMyAdmin/test/` and `phpMyAdmin/themes/` as listable.

![Screenshot 25 - DIRB listable-directory warnings (part 2)](Lab3-Screenshots/25_test3-dirb-listable-warnings-part2.png)

**Screenshot 25 - DIRB listable-directory warnings (part 2).** Final warnings: `twiki/bin/`, `twiki/lib/` and `twiki/pub/` **are listable**, so DIRB does not dig further into them.

![Screenshot 26 - DIRB scan completed](Lab3-Screenshots/26_test3-dirb-scan-complete-found-42.png)

**Screenshot 26 - DIRB scan completed.** DIRB finishes on **Sat Sep 5 22:54:13 2026** after about 38 seconds, after also reporting `phpMyAdmin/setup/frames/` and `setup/lib/` as listable. The summary reads **DOWNLOADED: 18448 - FOUND: 42**, so 42 accessible items were found. The prompt returns, showing the script completed normally.


With WhatWeb, Nmap and DIRB all completing successfully against the same authorised target, the assessment requirement that at least two tools execute successfully was comfortably met.

---

## Script Backup and Verification


![Screenshot 27 - Script copied to the Desktop and verified](Lab3-Screenshots/27_backup-script-copy-to-desktop.png)

**Screenshot 27 - Script copied to the Desktop and verified.** `cp recon_tool.sh ~/Desktop/` copies the finished script to the Desktop, and `ls ~/Desktop` confirms `recon_tool.sh` is there. `ls -l ~/Desktop/recon_tool.sh` (run twice) shows `-rwxrwxr-x 1 icdfa icdfa 1190 Sep 5 23:20 /home/icdfa/Desktop/recon_tool.sh`. The permissions still include execute rights, so the copy is ready to run and submit. The file is now **1190 bytes**, compared with 1030 bytes in the earlier permission check, which shows the script was edited after that first check (the `check_tool` function is the part not visible in the early editor screenshots).


The `recon_tool.sh` file was successfully copied to the Desktop and its presence was verified with `ls`. File permissions were also confirmed with `ls -l`, showing that the script remained executable and ready for submission.

---

## Observations From the Scan Results

These are reconnaissance observations about the lab target (Metasploitable2, an intentionally vulnerable machine). They were gathered only to understand its attack surface and were not exploited.

| Finding | Evidence (screenshots) | Why it matters |
|---|---|---|
| Many services exposed (23 open ports) | 10, 11, 12 | A larger attack surface; every unnecessary service should be disabled |
| Old, outdated software versions (vsftpd 2.3.4, OpenSSH 4.7p1, Apache 2.2.8, PHP 5.2.4, MySQL 5.0.51a) | 10, 11, 14 | Old versions often have publicly known vulnerabilities; version numbers help an attacker choose targets |
| Cleartext remote-access services (Telnet, rexec, rlogin) | 10 | Passwords and commands travel unencrypted; use SSH instead |
| A "bindshell" service on port 1524 (Metasploitable root shell) | 11 | Indicates a back door-style service; it should never be exposed |
| Database and remote-desktop services reachable (MySQL, PostgreSQL, VNC) | 11 | Should be limited to trusted hosts only |
| Web server discloses versions and technologies (Apache, PHP, WebDAV, X-Powered-By) | 14 | Information disclosure; hide banners and turn off unused modules |
| phpMyAdmin, `phpinfo.php`, `/test/`, `/dav/` and TWiki reachable | 17-25 | Admin tools, test scripts and directory listings should not be public |
| Directory listing enabled on many folders | 17, 20, 22, 25 | Lets anyone view file names and look for sensitive files |

---

## Complete Script

This is the final `recon_tool.sh`, put together from the editor screenshots (Screenshots 02 to 05).

```bash
#!/usr/bin/env bash

# Checks that a tool is installed before it is run.
# NOTE: this function is not visible in the screenshots; replace it with
# the exact version from your own recon_tool.sh if it differs.
check_tool() {
    if ! command -v "$1" >/dev/null 2>&1; then
        echo "Error: $1 is not installed."
        exit 1
    fi
}

echo "ICDFA Beginner Reconnaissance Tool"
echo "------------------------------------"
echo "Use only against authorized lab targets"
echo

read -rp "Enter authorised target IP address or domain: " target

echo "Target entered: $target"

if [[ -z "$target" ]]; then
    echo "Error: no target was entered."
    exit 1
fi

echo
echo "Select a reconnaissance tool:"
echo "1) WhatWeb"
echo "2) Nmap"
echo "3) DIRB"
echo "4) Exit"

read -rp "Enter your choice [1-4]: " choice

case "$choice" in
    1)
        check_tool whatweb
        echo "[+] Running WhatWeb against $target"
        whatweb "http://$target"
        ;;
    2)
        check_tool nmap
        echo "[+] Running Nmap service detection against $target"
        nmap -sV "$target"
        ;;
    3)
        check_tool dirb
        echo "[+] Running DIRB against $target"
        dirb "http://$target"
        ;;
    4)
        echo "Exiting. No scan was run."
        exit 0
        ;;
    *)
        echo "Error: invalid menu choice."
        exit 1
        ;;
esac
```

---

## Challenges Encountered

During this lab I met the following challenges:

- I mistakenly typed `ls l` instead of `ls -l`, which produced an error (Screenshot 06).
- Care was needed with the quotation marks around `"$target"` to avoid Bash syntax errors.
- Building the script step by step proved more effective than trying to write the whole program at once.

---

## What I Learned

From this lab, I learned:

- How to collect user input with `read -rp`.
- How to validate input with the `-z` operator.
- How to use functions in Bash.
- How to create menu-driven programs with the `case` statement.
- How to verify that a program is available using `command -v`.
- How to grant execute permission using `chmod +x`.
- How to automate reconnaissance activities with Bash scripting.
- The importance of conducting reconnaissance only against authorised systems.

---

## Answers to Understanding Questions

**1. What does the shebang do?**
It tells Linux which interpreter should run the script. Here `#!/usr/bin/env bash` runs it with Bash.

**2. What does `read -rp` do?**
It shows a prompt and stores what the user types in a variable. `-r` stops backslashes being treated specially and `-p` displays the prompt text.

**3. Why quote `"$target"`?**
Quoting safely handles spaces and special characters in the value, and stops Bash from splitting it into several words.

**4. What does `-z` test?**
It checks whether a string is empty (zero length).

**5. What is the purpose of `exit 1`?**
It stops the script and returns a non-zero status, which signals an error. (`exit 0` signals success.)

**6. Why is `case` suitable for menus?**
It gives a clean, readable way to handle several possible choices without a long chain of `if` statements.

**7. What does `;;` mean?**
It marks the end of a branch inside a `case` statement.

**8. What does `*` mean in a `case` statement?**
It is the default (catch-all) branch that matches anything not handled by an earlier option, such as an invalid menu choice.

**9. Why do WhatWeb and DIRB use `http://$target`?**
Both tools work against web servers and need the target in URL form, including the protocol.

**10. What does `nmap -sV` do?**
It detects the service and version running on each open port.

**11. What does `command -v` check?**
It checks whether a command exists, by looking it up in the system `PATH`, and prints its location if found.

**12. What does `chmod +x` do?**
It gives a file execute permission so it can be run as a program.

**13. What does `./` mean?**
It tells the shell to run the file from the current directory instead of searching the `PATH`.

**14. What does `tee` do?**
It displays command output on the screen and saves it to a file at the same time. (It was not used in the tests in this report, which printed results to the screen only.)

**15. Which requirement prevents hardcoding?**
The requirement that the target must be entered by the user at run time, which the script satisfies with `read -rp`.

---

## Conclusion

This lab showed how Bash scripting can automate reconnaissance through a simple menu-driven tool. The finished script asks the user for a target, validates the input, checks that the required tools are installed, and launches WhatWeb, Nmap or DIRB depending on the option chosen. Testing against the authorised target `10.15.203.91` confirmed that all three tools ran successfully. The exercise strengthened my understanding of Bash variables, functions, input validation, conditional statements and `case` logic, and reinforced the importance of authorisation and ethical conduct in security assessments.
