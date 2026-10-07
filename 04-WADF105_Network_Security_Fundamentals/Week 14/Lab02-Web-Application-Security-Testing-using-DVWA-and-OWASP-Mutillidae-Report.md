# Lab 2: Web Application Security Testing

| | |
|---|---|
| **Student Name** | Maryjudith Chidinma Ogunaka |
| **Registration Number** | C11/26/FCDF/17151 |
| **Programme** | ICDFA Trainee, Cohort 11 |
| **Lab Title** | Lab 2: Web Application Security Testing |
| **Date** | 06 September 2026 |
| **Authorised Targets** | DVWA and OWASP Mutillidae training applications |
| **DVWA Target IP Address** | 10.15.203.233 |
| **Mutillidae Target IP Address** | 10.234.230.91 |
| **Testing Machine** | Kali Linux virtual machine |

> **Authorisation note:** every test in this report was run against intentionally vulnerable training applications inside the authorised ICDFA lab environment. No system outside the lab was tested.

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Lab Environment and Tools](#lab-environment-and-tools)
3. [Part A: DVWA Setup and HTTP Inspection (Steps 1-4)](#part-a-dvwa-setup-and-http-inspection-steps-1-4)
4. [Part B: Burp Suite Setup and Interception (Steps 5-7)](#part-b-burp-suite-setup-and-interception-steps-5-7)
5. [Part C: File Upload Security Testing (Steps 8-13)](#part-c-file-upload-security-testing-steps-8-13)
6. [Part D: Web Server Assessment with Nikto (Steps 15-16)](#part-d-web-server-assessment-with-nikto-steps-15-16)
7. [Part E: Directory Discovery with DIRB (Steps 17-19)](#part-e-directory-discovery-with-dirb-steps-17-19)
8. [Part F: SQL Injection Concepts on Mutillidae (Steps 20-25)](#part-f-sql-injection-concepts-on-mutillidae-steps-20-25)
9. [Part G: Automated SQL Injection Testing with SQLMap (Steps 26-29)](#part-g-automated-sql-injection-testing-with-sqlmap-steps-26-29)
10. [Final Report](#final-report)
11. [Findings Summary](#findings-summary)
12. [Defensive Recommendations](#defensive-recommendations)
13. [Student Questions](#student-questions)

---

## Executive Summary

This lab assessed web application security using two intentionally vulnerable applications, DVWA (Damn Vulnerable Web Application) and OWASP Mutillidae, inside an authorised training environment. The goal was to gain practical experience with common web security assessment techniques and to understand how weaknesses can affect the confidentiality, integrity and availability of information systems.

The assessment started by examining how DVWA communicates with the browser, using Firefox developer tools and Burp Suite. HTTP requests, responses, cookies and parameters were inspected. File upload controls were then tested with a normal upload and an extension-handling test.

Web server assessment followed. Nikto was used to report server configuration observations and potential weaknesses, and DIRB was used to discover web-accessible directories and files. Together they showed the structure of the target and why secure server configuration and content management matter.

The second phase used the Mutillidae login form to study SQL injection concepts. User-controlled inputs were identified, a baseline response was recorded, and special SQL characters and logical conditions were submitted to see how the application reacted. The application returned verbose SQL error messages that exposed the structure of the query. SQLMap was then used to automate testing of the `page` parameter. It tested the parameter with several techniques but did not confirm an injectable parameter, which shows that automated tools complement manual testing but do not replace it.

During the exercise the target virtual machine received different IP addresses because of dynamic (DHCP) addressing in the virtual network. Testing began on `10.15.203.233` (DVWA) and later continued on `10.234.230.91` (Mutillidae) after connectivity was re-established. This did not affect the assessment because all testing stayed within the same authorised lab environment.

Overall, the lab gave practical experience in web application assessment, file upload security, server enumeration, directory discovery, SQL injection concepts and automated vulnerability testing. It reinforced secure coding, server-side validation, prepared statements, proper error handling, secure file storage and authorised testing procedures.

---

## Lab Environment and Tools

| Tool | Purpose in this lab |
|---|---|
| Kali Linux (VirtualBox/VMware VM) | Attacker/testing machine used for every step |
| Metasploitable 2 VM | Target machine hosting DVWA and Mutillidae |
| Firefox + Developer Tools | Inspecting requests, responses and cookies |
| Burp Suite Community Edition | Intercepting proxy for viewing HTTP traffic |
| Nikto | Web server configuration and known-issue scanner |
| DIRB | Directory and file discovery by wordlist |
| SQLMap | Automated SQL injection detection |


![Screenshot 01 - Target virtual machine network configuration](Lab2-Screenshots/01_target-vm-ifconfig.png)

**Screenshot 01 - Target virtual machine network configuration.** This is the console of the Metasploitable 2 target VM after running `ifconfig`. The `eth0` interface shows an IPv4 address on the `10.234.230.0/24` network (mask `255.255.255.0`, broadcast `10.234.230.255`), an IPv6 link-local address, and traffic counters with zero errors or dropped packets. It shows that the target was up and reachable on the lab network, and it supports the note that the target's address changed (from `10.15.203.233` to the `10.234.230.x` range) because the virtual network assigns addresses dynamically. Because the address can change, the target IP was re-checked before each phase of testing.


---

## Part A: DVWA Setup and HTTP Inspection (Steps 1-4)

### Step 1: Confirm the target is reachable and open DVWA

**Objective:** make sure the authorised target responds before any testing begins.

**Command used:** `ping 10.15.203.233 -c 4`


![Screenshot 02 - Ping test to the DVWA target](Lab2-Screenshots/02_step01-ping-dvwa-target.png)

**Screenshot 02 - Ping test to the DVWA target.** The terminal on Kali sends four ICMP echo requests to `10.15.203.233`. All four packets were transmitted and received with **0% packet loss**, a TTL of 64 (typical of a Linux host) and response times between 0.508 ms and 1.79 ms (average about 1.36 ms). This proves the target machine was online and that the network path between Kali and the target worked, so any later failure could not be blamed on basic connectivity.

![Screenshot 03 - DVWA login page](Lab2-Screenshots/03_step01-dvwa-login-page.png)

**Screenshot 03 - DVWA login page.** Firefox shows the DVWA login page at `http://10.15.203.233/dvwa/login.php`, with the Username and Password fields and the DVWA logo. The page loaded without errors, which confirms the web service on port 80 was running. The address bar also records exactly which host was being tested, helping to keep testing inside the approved lab.

![Screenshot 04 - DVWA home page after logging in](Lab2-Screenshots/04_step01-dvwa-home-logged-in.png)

**Screenshot 04 - DVWA home page after logging in.** After signing in, the DVWA welcome page (`index.php`) appears. The left menu lists the vulnerability modules (Brute Force, Command Execution, CSRF, File Inclusion, SQL Injection, Upload, XSS and others), and the message box at the bottom shows **You have logged in as 'admin'**. The page also carries DVWA's warning that it must never be installed on a public web server. This screenshot confirms authenticated access to the application, which was needed for the upload exercise.


**Report:** DVWA was reached in Firefox using the assigned target IP address. The login page loaded correctly with no connectivity or server errors, confirming the web service was active. Verifying the URL before testing ensured every later action stayed inside the approved laboratory environment.

### Step 2: Set the DVWA security level to Low

**Objective:** create a predictable, intentionally weak environment so the upload and SQL injection exercises behave as the lab expects.


![Screenshot 05 - DVWA security level before the change (High)](Lab2-Screenshots/05_step02-dvwa-security-level-high.png)

**Screenshot 05 - DVWA security level before the change (High).** The DVWA Security page (`security.php`) reports **Security Level is currently high**, with a drop-down set to choose low, medium or high. PHPIDS is shown as disabled. This is the starting state, recorded as evidence of what was changed.

![Screenshot 06 - DVWA security level set to Low](Lab2-Screenshots/06_step02-dvwa-security-level-set-low.png)

**Screenshot 06 - DVWA security level set to Low.** After choosing `low` and pressing Submit, the page now reads **Security Level is currently low** and shows the confirmation message **Security level set to low** at the bottom. Low means DVWA applies almost no input validation, which makes the vulnerabilities easy to observe safely.


**Report:** From the DVWA Security page, the level was changed to Low and submitted. The page refreshed and displayed Low as the active setting, confirming the change. This configuration ensured the file upload and SQL injection exercises behaved as expected.

### Step 3: Open the browser Network tools

**Objective:** observe normal browser-to-server communication before using Burp Suite, so there is a baseline to compare against.


![Screenshot 07 - Firefox Developer Tools Inspector opened](Lab2-Screenshots/07_step03-devtools-inspector-open.png)

**Screenshot 07 - Firefox Developer Tools Inspector opened.** The Developer Tools panel is docked at the bottom of the DVWA Security page, with the **Inspector** tab active. It shows the page's HTML structure (`<html>`, `<body class="home">`, `<div id="container">`) and the CSS rules applied. This shows that a browser exposes the HTML it receives, which is the content a tester can examine.

![Screenshot 08 - Network tab opened but empty](Lab2-Screenshots/08_step03-devtools-network-tab-empty.png)

**Screenshot 08 - Network tab opened but empty.** The **Network** tab is now selected. It reports **No requests** and asks the user to reload the page to see network activity. The tab only records traffic after it is open, so the page had to be reloaded to capture anything.

![Screenshot 09 - Network tab after reloading the page](Lab2-Screenshots/09_step03-network-tab-requests-after-reload.png)

**Screenshot 09 - Network tab after reloading the page.** After reloading, five requests appear, all with status **200** and method **GET** to `10.15.203.233`: `security.php` (the HTML document), `dvwaPage.js` (script), `logo.png`, `lock.png` and `favicon.ico`. The footer shows **5 requests, 15.98 kB / 4.45 kB transferred, finish 400 ms, DOMContentLoaded 134 ms, load 148 ms**. This is the normal traffic pattern for one DVWA page: one document request followed by the files that page needs.

![Screenshot 10 - Request headers for security.php](Lab2-Screenshots/10_step03-network-request-headers.png)

**Screenshot 10 - Request headers for security.php.** Selecting the `security.php` request and opening the **Headers** tab shows `GET http://10.15.203.233/dvwa/security.php` with **Status 200 OK**, **Version HTTP/1.1** and the referrer policy `strict-origin-when-cross-origin`. This identifies the request method, URL, status code and headers, the four main parts of an HTTP exchange.


**Report:** The Network tab was used to watch communication between the browser and DVWA. After reloading, several requests and responses appeared. The request to the DVWA Security page was examined for its method, URL, status code and headers. Observing normal traffic before using Burp Suite gave a baseline understanding of the application's standard behaviour.

### Step 4: Identify cookies

**Objective:** find out how the application tracks the user's session and settings.


![Screenshot 11 - Cookies sent with the DVWA request](Lab2-Screenshots/11_step04-network-request-cookies.png)

**Screenshot 11 - Cookies sent with the DVWA request.** The **Cookies** sub-tab of the `security.php` request lists the *Request Cookies*. Two cookie names are present: **PHPSESSID**, the PHP session identifier that tells the server which logged-in user is making the request, and **security**, which stores the DVWA security level chosen in Step 2 (its value is `low`). In a formal report the cookie *values* should be hidden, because a session ID can be reused to hijack a session.


**Report:** Two cookie names were observed, `PHPSESSID` and `security`. They let the server recognise the authenticated user and keep the application state across requests. To prevent session disclosure, only the cookie names are recorded in this report and the actual values are deliberately excluded.

---

## Part B: Burp Suite Setup and Interception (Steps 5-7)

### Step 5: Start Burp Suite

**Objective:** install and launch Burp Suite, an intercepting proxy, so HTTP requests can be viewed before they reach the server.


![Screenshot 12 - Burp Suite not installed, install requested](Lab2-Screenshots/12_step05-burpsuite-install-command.png)

**Screenshot 12 - Burp Suite not installed, install requested.** Typing `burpsuite` returns **Command 'burpsuite' not found** with a suggestion to install it using `sudo apt install burpsuite`. The command was accepted with `y`, and the terminal then asks for the sudo password. The output also lists unused packages that `apt autoremove` could clean up. This shows Burp was not preinstalled and had to be added.

![Screenshot 13 - Burp Suite installation completed](Lab2-Screenshots/13_step05-burpsuite-install-complete.png)

**Screenshot 13 - Burp Suite installation completed.** The installer reports **Installing: 1** package (`burpsuite`), a **344 MB** download and **361 MB** of disk space needed. It fetched the package from the Kali repository, unpacked and set up `burpsuite 2026.8-0kali1` and returned to the prompt without errors. This confirms a successful installation.

![Screenshot 14 - Burp Suite terms and conditions](Lab2-Screenshots/14_step05-burpsuite-terms-and-conditions.png)

**Screenshot 14 - Burp Suite terms and conditions.** On first launch, Burp displays the PortSwigger **Terms and Conditions** with **I Decline** and **I Accept** buttons. Accepting is required before the tool can be used.

![Screenshot 15 - Choosing a Burp Suite edition](Lab2-Screenshots/15_step05-burpsuite-edition-selection.png)

**Screenshot 15 - Choosing a Burp Suite edition.** Burp offers two options: **Burp Suite Professional** (paid, with features such as full-speed Intruder, project files and Collaborator) and **Burp Suite Community Edition** (the free manual toolkit, described as ideal for learning). The Community Edition was selected, which is enough for this lab.

![Screenshot 16 - Creating a temporary project](Lab2-Screenshots/16_step05-burpsuite-community-temporary-project.png)

**Screenshot 16 - Creating a temporary project.** The Burp Suite Community Edition v2026.8 start window offers **Temporary project in memory**, which is the option selected. *New project on disk* is marked **Project files are Burp Suite Professional only**. Because the Community Edition cannot save project files, a temporary project is used and all data disappears when Burp is closed. A Java warning (`No JAVA_CMD set for run_java, falling back to JAVA_CMD = java`) is visible in the terminal behind the window and is harmless.

![Screenshot 17 - Selecting Burp's default configuration](Lab2-Screenshots/17_step05-burpsuite-use-defaults-start.png)

**Screenshot 17 - Selecting Burp's default configuration.** The next window asks which configuration to load. **Use Burp defaults** is selected, and **Start Burp** is clicked to continue. Defaults avoid unexpected custom settings.

![Screenshot 18 - Burp Suite Getting Started page](Lab2-Screenshots/18_step05-burpsuite-getting-started.png)

**Screenshot 18 - Burp Suite Getting Started page.** Burp opened to its **Getting started** page, which explains setting the target scope and mapping the website. The tool bar across the top shows the modules available (Dashboard, Target, Proxy, Intruder, Repeater, Collaborator, Sequencer, Decoder, Comparer, Logger, Organizer and Extensions). The memory indicator at the bottom shows Burp is running normally.


**Report:** Burp Suite was launched successfully on Kali Linux using a temporary project for the duration of the exercise. It gave access to Proxy, Target, Repeater, Intruder and the other testing modules. Burp works as an intercepting proxy that lets HTTP requests and responses be viewed, intercepted and analysed before they reach the target, which helps a tester understand how user input is sent and processed.

### Step 6: Open the Burp browser

**Objective:** use Burp's built-in browser so that traffic is routed through the proxy automatically, with no manual proxy settings.


![Screenshot 19 - Burp Suite dashboard](Lab2-Screenshots/19_step06-burpsuite-dashboard.png)

**Screenshot 19 - Burp Suite dashboard.** The **Dashboard** shows one running task, **Live passive crawl from Proxy (all traffic)**, with *Capturing* switched on. The site map is empty (**No items to show**) and the counters show 0 items added, 0 responses processed. This means Burp is ready and listening but has not yet seen any traffic.

![Screenshot 20 - Proxy tab with interception off](Lab2-Screenshots/20_step06-burp-proxy-intercept-off.png)

**Screenshot 20 - Proxy tab with interception off.** In the **Proxy > Intercept** tab, the button reads **Intercept off** and the page explains that turning it on will hold messages between Burp's browser and target servers. An **Open browser** button is available. Interception is left off at first so normal browsing is not blocked.

![Screenshot 21 - Burp's built-in browser opened](Lab2-Screenshots/21_step06-burp-browser-opened.png)

**Screenshot 21 - Burp's built-in browser opened.** Clicking **Open browser** launches a Chromium window (tab titled *PortSwigger*). Its address bar is where the DVWA URL is typed. Because this browser is pre-configured to send everything through Burp, nothing needs to be changed in Firefox's proxy settings.


**Report:** The built-in Burp browser was opened from the Proxy module to simplify interception and avoid proxy-configuration errors. It was automatically configured to route traffic through Burp, so requests and responses could be captured without extra setup. It was used for the remaining Burp activities.

### Step 7: Intercept a harmless request

**Objective:** capture one ordinary request before it reaches the server and understand what it contains.


![Screenshot 22 - Intercept switched off before the test](Lab2-Screenshots/22_step07-burp-intercept-off.png)

**Screenshot 22 - Intercept switched off before the test.** The Proxy > Intercept tab again shows **Intercept is off**. This is the starting state before the test.

![Screenshot 23 - Intercept switched on](Lab2-Screenshots/23_step07-burp-intercept-on.png)

**Screenshot 23 - Intercept switched on.** The button now reads **Intercept on** and the message explains that messages between Burp's browser and the target servers are held here, so they can be analysed and changed before forwarding. From this point the browser pauses on every request until it is forwarded or dropped.

![Screenshot 24 - First intercepted request (sent to Google search)](Lab2-Screenshots/24_step07-burp-intercepted-request-google-search.png)

**Screenshot 24 - First intercepted request (sent to Google search).** The first request held by Burp is a `GET /search?q=http%3A%2F%2F10.15.203.233%2Fdvwa%2F...` to `www.google.com`. In other words, the DVWA address typed in the Burp browser was treated as a *search term* instead of a web address. The Raw view shows the request line, `Host: www.google.com`, `Sec-Ch-Ua` browser headers and `Accept-Language`. The Inspector panel on the right counts request attributes, query parameters (6), body parameters (0), cookies (0) and headers. It is still a useful example of how a GET request carries its data in the URL query string.

![Screenshot 25 - Intercepted DVWA GET request](Lab2-Screenshots/25_step07-burp-intercepted-dvwa-get-request.png)

**Screenshot 25 - Intercepted DVWA GET request.** After entering the address correctly, Burp holds a `GET /dvwa/ HTTP/1.1` request to `http://10.15.203.233:80`. The Raw view shows the **method** (GET), **path** (`/dvwa/`), **Host** header (`10.15.203.233`), **Accept-Language**, `Upgrade-Insecure-Requests` and the **User-Agent** string (Chrome on Linux). The Inspector shows no query or body parameters and no cookies, confirming this is a plain page request. Clicking **Forward** released it so DVWA could load normally.


**Report:** Burp was set to intercept traffic between the browser and DVWA. After enabling interception and browsing to the DVWA URL, a normal GET request was captured before being forwarded. It showed the request method, path, Host header and User-Agent header. Examining it explained how browsers communicate with web applications, and after review the request was forwarded so DVWA loaded normally.

---

## Part C: File Upload Security Testing (Steps 8-13)

### Step 8: Create a harmless test file

**Objective:** make a safe file for upload testing that contains no executable code.

**Commands used:**

```bash
echo 'ICDFA beginner upload test' > icdfa-upload-test.txt
ls -l icdfa-upload-test.txt
```


![Screenshot 26 - Creating and verifying the test file](Lab2-Screenshots/26_step08-create-harmless-test-file.png)

**Screenshot 26 - Creating and verifying the test file.** The `echo` command writes the sentence *ICDFA beginner upload test* into `icdfa-upload-test.txt`, and `ls -l` confirms the file exists. It is a regular file (`-rw-rw-r--`) owned by the `icdfa` user and **27 bytes** in size. The content is plain text only, so it is safe to use as the baseline upload object.


**Report:** A harmless text file was created for file upload testing in DVWA. It contained only simple text and no executable code. The `ls -l` command confirmed its name and size. It served as the baseline object for later validation tests.

### Step 10: Normal upload

**Objective:** upload an ordinary file, unchanged, to learn how the application normally responds.


![Screenshot 27 - First upload attempt returned an error](Lab2-Screenshots/27_step10-dvwa-normal-upload-first-attempt-failed.png)

**Screenshot 27 - First upload attempt returned an error.** In **Vulnerability: File Upload**, `icdfa-upload-test.txt` was chosen with the Browse button and Upload was clicked. The page replied in red **Your image was not uploaded.** This first attempt did not succeed, and the result was recorded before trying again.

![Screenshot 28 - Normal upload succeeded](Lab2-Screenshots/28_step10-dvwa-normal-upload-success.png)

**Screenshot 28 - Normal upload succeeded.** On the next attempt the page shows **../../hackable/uploads/icdfa-upload-test.txt succesfully uploaded!** (the spelling comes from DVWA itself). The message reveals the **server-side storage path**: uploaded files go to the `hackable/uploads` folder inside the DVWA directory. It also shows that a `.txt` file was accepted even though the form says *Choose an image to upload*, which suggests DVWA at Low level does not restrict file types strictly.


**Report:** A normal upload test was done with `icdfa-upload-test.txt`, submitted without changing any part of the request. The application processed it and returned a message showing the outcome and storage location. This established a baseline for comparing later tests with different extensions and MIME types.

### Step 11: Intercept the upload request

**Objective:** understand how a browser packages a file when sending it to a web application.


![Screenshot 29 - Burp browser address bar while entering the DVWA address](Lab2-Screenshots/29_step11-burp-browser-dvwa-url-entry.png)

**Screenshot 29 - Burp browser address bar while entering the DVWA address.** The Burp browser's address bar is being used to enter the DVWA address (`http://10.15.203.233/...`) with the browser suggesting the base address `http://10.15.203.233` below it. This is the step used to open DVWA inside the Burp browser so that the upload could be routed through the proxy.

![Screenshot 30 - Upload page with Burp running in the background](Lab2-Screenshots/30_step11-dvwa-upload-success-burp-session.png)

**Screenshot 30 - Upload page with Burp running in the background.** DVWA's File Upload page again shows the success message for `icdfa-upload-test.txt`. The taskbar now shows Burp Suite and the Burp browser running together with Firefox. This is the upload performed while Burp was active, so the browser-to-server exchange passed through the proxy.


**How a file upload request is built (explanation):** a file upload travels as an HTTP **POST** request with the content type `multipart/form-data`. The body is divided into parts: one part for the file (with its `filename` and the client-supplied `Content-Type`) and others for any extra form fields. Both the filename and the content type are chosen by the *client*, so the server must never trust them without its own checks.

**Report:** The upload process was examined with Burp Suite to understand how files are sent to a web application. An upload is an HTTP POST with multipart form data that includes the filename, the form field responsible for the upload and the client-supplied MIME type. This showed why server-side validation is necessary.

### Step 12: Extension handling

**Objective:** test whether the application checks the file extension.

**Command used:** `cp icdfa-upload-test.txt icdfa-upload.test.log`


![Screenshot 31 - Copying the test file to a .log extension](Lab2-Screenshots/31_step12-copy-file-to-log-extension.png)

**Screenshot 31 - Copying the test file to a .log extension.** The `cp` command makes a copy of the text file with a different extension (`.log`). The file content is unchanged, so only the extension differs. This isolates the extension as the single variable being tested.

![Screenshot 32 - The .log file was accepted](Lab2-Screenshots/32_step12-dvwa-upload-log-success.png)

**Screenshot 32 - The .log file was accepted.** DVWA responds **../../hackable/uploads/icdfa-upload.test.log succesfully uploaded!** The application accepted a file with a different extension without objection. This shows its upload controls are permissive: the extension alone was not used to reject the file.


**Report:** A copy of the text file was renamed from `.txt` to `.log` and uploaded. The application accepted it and showed a success message, showing the extension was not strictly validated and the controls were permissive.

### Step 13: MIME handling

**Objective:** show that the MIME type (the `Content-Type` value) sent by the browser is not a trustworthy security control.


![Screenshot 33 - Upload test for MIME-type handling](Lab2-Screenshots/33_step13-dvwa-upload-txt-mime-test.png)

**Screenshot 33 - Upload test for MIME-type handling.** The File Upload page again shows `icdfa-upload-test.txt` successfully uploaded, this time at a later point in the session (10:52). This upload was the starting point for comparing the file's real content (plain text) with the MIME type the browser sent along with it.

![Screenshot 34 - Burp HTTP history](Lab2-Screenshots/34_step13-burp-http-history.png)

**Screenshot 34 - Burp HTTP history.** The **Proxy > HTTP history** tab lists requests captured by Burp, with columns for host, method, URL, status code, length, MIME type, title, TLS, IP and cookies. The entries here are to `www.google.com` (Google Search, a 302 redirect, a 429 response and reCAPTCHA pages) left over from the earlier address-bar search. It shows how Burp logs traffic and where a tester would look to review a past request.


**Report:** The purpose of this test was to demonstrate that the MIME type supplied by the browser can be changed and so should not be trusted as a security control. A client can easily change the `Content-Type` header before the request reaches the server, making MIME-based validation unreliable on its own. Effective upload validation should include server-side content inspection as well as MIME checks.

---

## Part D: Web Server Assessment with Nikto (Steps 15-16)

### Step 15: Run Nikto

**Objective:** scan the web server for configuration problems, outdated software and risky default files.

**Command used:** `nikto -h http://10.15.203.233`


![Screenshot 35 - Nikto not installed, install requested](Lab2-Screenshots/35_step15-nikto-install-command.png)

**Screenshot 35 - Nikto not installed, install requested.** Running `nikto` returns **Command 'nikto' not found** and the install suggestion `sudo apt install nikto`, which was accepted. The unused-package list is shown before the install starts.

![Screenshot 36 - Packages to be upgraded for the Nikto install](Lab2-Screenshots/36_step15-nikto-install-upgrade-list.png)

**Screenshot 36 - Packages to be upgraded for the Nikto install.** The installer lists the existing packages it must upgrade (`dpkg`, `libc6`, several Perl libraries and others) because Nikto needs newer dependencies.

![Screenshot 37 - Install confirmation prompt](Lab2-Screenshots/37_step15-nikto-install-confirm-prompt.png)

**Screenshot 37 - Install confirmation prompt.** The summary shows **Upgrading: 36, Installing: 15, Download size: 33.2 MB, Space needed: 61.5 MB** and waits for `Continue? [Y/n]`. The user confirms with `y`.

![Screenshot 38 - Downloading packages](Lab2-Screenshots/38_step15-nikto-install-downloading.png)

**Screenshot 38 - Downloading packages.** Package files such as `dpkg`, `libc6` and `locales` are downloaded from the Kali repository (`kali.download`). This confirms the Kali machine had internet access to the repository.

![Screenshot 39 - Installation progress at 99%](Lab2-Screenshots/39_step15-nikto-install-progress.png)

**Screenshot 39 - Installation progress at 99%.** The log shows Perl XML parser modules being registered and **Setting up nikto (1:2.6.1-0kali1)**, with the progress bar at 99%. The install was nearly finished.

![Screenshot 40 - Nikto installed](Lab2-Screenshots/40_step15-nikto-install-complete.png)

**Screenshot 40 - Nikto installed.** The final lines show `Setting up nikto` and the post-install triggers, then the prompt returns with no errors. Nikto is now ready to use.

![Screenshot 41 - Nikto scan started](Lab2-Screenshots/41_step15-nikto-scan-start.png)

**Screenshot 41 - Nikto scan started.** `nikto -h http://10.15.203.233` begins the scan with **Nikto v2.6.1**. The header lists **Target IP 10.15.203.233, Port 80** and start time `2026-09-05 11:20:37`. First findings: the server is **Apache/2.2.8 (Ubuntu) DAV/2**, the `x-powered-by` header reveals **PHP/5.2.4-2ubuntu5.10**, and **directory indexing** is enabled on `/icons/`.

![Screenshot 42 - Nikto findings: TRACE, mod_negotiation and outdated software](Lab2-Screenshots/42_step15-nikto-scan-findings-1.png)

**Screenshot 42 - Nikto findings: TRACE, mod_negotiation and outdated software.** More findings scroll past: the **HTTP TRACE method is active** (which suggests exposure to Cross-Site Tracing), an uncommon `tcn` header, **Apache mod_negotiation with MultiViews** (which helps an attacker guess file names), and warnings that **PHP 5.2.4 and Apache 2.2.8 appear to be outdated** (current versions are far newer). A missing `X-Content-Type-Options` header is also reported.

![Screenshot 43 - Nikto scan completed](Lab2-Screenshots/43_step15-nikto-scan-complete-31-items.png)

**Screenshot 43 - Nikto scan completed.** The end of the scan shows the deprecated `X-Frame-Options` header notice and the missing `X-Content-Type-Options` header, which could let a browser interpret content in a different way. The summary reads **8235 requests: 0 errors and 31 items reported**, finishing in 31 seconds, with **1 host tested**.


**Report:** A Nikto scan ran against the target web server to find common security issues and configuration weaknesses. Nikto connected successfully and reported several observations: information disclosure through server responses (versions in headers), directories and files that may be accessible, missing or weak security headers, and signs of default configuration. These results are *observations* until verified manually, but they highlight the importance of web server hardening.

### Step 16: Save the Nikto output

**Objective:** keep the scan results as evidence.

**Command used:** `nikto -h http://10.15.203.233 -output lab2-nikto.txt`


![Screenshot 44 - Nikto re-run with output saved to a file](Lab2-Screenshots/44_step16-nikto-save-output-command.png)

**Screenshot 44 - Nikto re-run with output saved to a file.** The same scan is repeated with the `-output lab2-nikto.txt` option so the results are written to a text file as well as printed on screen. The scan header and the first findings match the earlier scan (start time `11:27:50`).

![Screenshot 45 - Saved scan: further header findings](Lab2-Screenshots/45_step16-nikto-saved-scan-findings-1.png)

**Screenshot 45 - Saved scan: further header findings.** The scan output continues with the TRACE, `tcn`, MultiViews and outdated PHP/Apache findings, and also reports that the suggested **Permissions-Policy** security header is missing.

![Screenshot 46 - Saved scan: browsable directory and PHP Easter eggs](Lab2-Screenshots/46_step16-nikto-saved-scan-findings-2.png)

**Screenshot 46 - Saved scan: browsable directory and PHP Easter eggs.** More findings: the `/doc/` directory is **browsable** (directory indexing, CWE-548) and several **PHP Easter egg** requests (special query strings such as `/?=PHPE9568F36-...`) reveal information when the server runs an old PHP version. These are classic information-disclosure issues.

![Screenshot 47 - Saved scan: phpinfo and phpMyAdmin exposure](Lab2-Screenshots/47_step16-nikto-saved-scan-findings-3.png)

**Screenshot 47 - Saved scan: phpinfo and phpMyAdmin exposure.** Final findings include **`/phpinfo.php`** (a test script that prints detailed PHP and system information), the Apache default file `/icons/README`, and the **phpMyAdmin** `Documentation.html` and `README`, which warn that the database management tool should be protected or limited to authorised hosts. The end of the output is shown with the deprecated X-Frame-Options and missing X-Content-Type-Options notices.

![Screenshot 48 - Nikto output file confirmed](Lab2-Screenshots/48_step16-nikto-output-file-ls.png)

**Screenshot 48 - Nikto output file confirmed.** The scan finishes with **8235 requests, 0 errors and 31 items** in 173 seconds (end time `11:30:43`). `ls -l lab2-nikto.txt` then shows the report file exists, **4459 bytes** in size, created on Sep 5 at 11:30, proving the evidence was saved.


**Report:** The Nikto results were saved to `lab2-nikto.txt` for documentation and later review. The file holds the server details, configuration observations and potential security concerns found during the scan, and it was kept as supporting evidence for the assessment.

---

## Part E: Directory Discovery with DIRB (Steps 17-19)

### Step 17: Run DIRB

**Objective:** find hidden or unlinked directories and files by trying names from a wordlist.

**Command used:** `dirb http://10.15.203.233`


![Screenshot 49 - DIRB not installed, install requested](Lab2-Screenshots/49_step17-dirb-install-command.png)

**Screenshot 49 - DIRB not installed, install requested.** Running `dirb` returns **Command 'dirb' not found** with the suggestion `sudo apt install dirb`. The install is accepted and the sudo password requested.

![Screenshot 50 - DIRB install summary](Lab2-Screenshots/50_step17-dirb-install-summary.png)

**Screenshot 50 - DIRB install summary.** A small install: **1 package, 208 kB download, 1,511 kB of disk space**. The package `dirb 2.22+dfsg-7` is fetched from the Kali repository.

![Screenshot 51 - DIRB installed](Lab2-Screenshots/51_step17-dirb-install-complete.png)

**Screenshot 51 - DIRB installed.** `dirb` is unpacked and set up and the prompt returns without errors. DIRB is ready.

![Screenshot 52 - DIRB scan started](Lab2-Screenshots/52_step17-dirb-scan-start.png)

**Screenshot 52 - DIRB scan started.** `dirb http://10.15.203.233` starts **DIRB v2.22**. The header shows `URL_BASE: http://10.15.203.233/` and the wordlist `/usr/share/dirb/wordlists/common.txt`.

![Screenshot 53 - DIRB results: root directory](Lab2-Screenshots/53_step17-dirb-scan-results-01.png)

**Screenshot 53 - DIRB results: root directory.** DIRB reports **4612 generated words** and starts scanning the root. Results: `/cgi-bin/` (**403 Forbidden**), the directory `/dav/`, `/index` and `/index.php` (200), `/phpinfo` and `/phpinfo.php` (200, about 48 KB), the directory `/phpMyAdmin/` and `/server-status` (403). A `200` means the page exists and can be opened; a `403` means it exists but access is blocked.

![Screenshot 54 - DIRB results: test, twiki and dav](Lab2-Screenshots/54_step17-dirb-scan-results-02.png)

**Screenshot 54 - DIRB results: test, twiki and dav.** Further directories found: `/test/` and `/twiki/`. DIRB warns that `/dav/` **is listable** (its contents can be viewed), so it skips scanning it. It then enters `/phpMyAdmin/` and finds `calendar`, `changelog` and `ChangeLog`.

![Screenshot 55 - DIRB results: phpMyAdmin contents (1)](Lab2-Screenshots/55_step17-dirb-scan-results-03.png)

**Screenshot 55 - DIRB results: phpMyAdmin contents (1).** Inside phpMyAdmin DIRB finds `contrib/`, `docs`, `error`, `export`, `favicon.ico`, `import`, `index.php`, `js/`, `lang/`, `libraries/`, `license` and `main`, most returning **200**. A web-accessible database administration tool with visible documentation is a notable exposure.

![Screenshot 56 - DIRB results: phpMyAdmin contents (2)](Lab2-Screenshots/56_step17-dirb-scan-results-04.png)

**Screenshot 56 - DIRB results: phpMyAdmin contents (2).** More phpMyAdmin items: `navigation`, `phpinfo`, `phpmyadmin`, `print`, `readme`, `README`, **`robots.txt`**, `scripts/`, `setup/`, `sql` and `test/`. The `setup/` directory is especially sensitive because it is used to configure phpMyAdmin.

![Screenshot 57 - DIRB results: themes and twiki](Lab2-Screenshots/57_step17-dirb-scan-results-05.png)

**Screenshot 57 - DIRB results: themes and twiki.** DIRB lists `themes/`, `TODO`, `webapp`, then notes that `/test/` is listable and starts on `/twiki/`, where `/twiki/bin/` and `/twiki/data` (**403**) appear.

![Screenshot 58 - DIRB results: twiki files](Lab2-Screenshots/58_step17-dirb-scan-results-06.png)

**Screenshot 58 - DIRB results: twiki files.** Found in TWiki: `index`, `index.html`, `lib/` with `license`, and `pub/` with `readme` (200) and `templates` (403). `phpMyAdmin/contrib/` is flagged as listable.

![Screenshot 59 - DIRB results: listable directories (1)](Lab2-Screenshots/59_step17-dirb-scan-results-07.png)

**Screenshot 59 - DIRB results: listable directories (1).** DIRB repeats the `pub/` findings and shows warnings that `phpMyAdmin/contrib/`, `js/` and `lang/` **are listable**. Directory listing lets anyone view file names in a folder.

![Screenshot 60 - DIRB results: listable directories (2)](Lab2-Screenshots/60_step17-dirb-scan-results-08.png)

**Screenshot 60 - DIRB results: listable directories (2).** More **listable** warnings: `phpMyAdmin/js/`, `lang/` and `libraries/`. Directory listing is repeated across this application, which indicates a configuration issue rather than a single mistake.

![Screenshot 61 - DIRB results: phpMyAdmin setup](Lab2-Screenshots/61_step17-dirb-scan-results-09.png)

**Screenshot 61 - DIRB results: phpMyAdmin setup.** Inside `phpMyAdmin/setup/` DIRB finds `config` (status **303**, a redirect), `frames/`, `index` and `index.php` (200), `lib/`, `scripts` and `styles`. A reachable setup area is a risk if it is not removed after installation.

![Screenshot 62 - DIRB results: more listable directories](Lab2-Screenshots/62_step17-dirb-scan-results-10.png)

**Screenshot 62 - DIRB results: more listable directories.** DIRB finishes `setup/styles`, then reports `phpMyAdmin/test/`, `themes/`, `twiki/bin/` and `twiki/lib/` as listable.

![Screenshot 63 - DIRB results: twiki/pub and setup folders](Lab2-Screenshots/63_step17-dirb-scan-results-11.png)

**Screenshot 63 - DIRB results: twiki/pub and setup folders.** `twiki/pub/`, `phpMyAdmin/setup/frames/` and `phpMyAdmin/setup/lib/` are also listable, which completes the crawl of discovered directories.

![Screenshot 64 - DIRB scan completed](Lab2-Screenshots/64_step17-dirb-scan-complete-found-42.png)

**Screenshot 64 - DIRB scan completed.** DIRB ends with **DOWNLOADED: 18448 - FOUND: 42**: it sent 18,448 requests and found 42 accessible items. This is the full picture of the web structure discovered on the target.


**Report:** A DIRB scan was run with `dirb http://10.15.203.233` to find hidden directories, files and resources not linked from the main website. It found several accessible paths and returned different status codes: **200** (found), **303** (redirect) and **403** (forbidden). The results showed the application's structure and highlighted sensitive locations such as phpMyAdmin, phpinfo, `/test/`, `/dav/` and TWiki. The scan shows how content-discovery tools help testers understand the attack surface.

### Step 18: Relate DIRB to uploads

During the DVWA file upload exercise, files were stored in the `hackable/uploads` directory, so the application keeps a dedicated location for uploaded content. DIRB's results were compared with this observation to see which discovered paths related to storage. This showed how the application organises uploaded files and why upload directories must be locked down to prevent unauthorised access or execution of uploaded content. (No separate screenshot was taken for this step.)

### Step 19: Save the DIRB output

**Objective:** keep the directory discovery results as evidence.

**Command used:** `dirb http://10.15.203.233 -o lab2-dirb.txt`


![Screenshot 65 - DIRB re-run with output saved](Lab2-Screenshots/65_step19-dirb-save-output-command.png)

**Screenshot 65 - DIRB re-run with output saved.** The scan is repeated with `-o lab2-dirb.txt`. The header now shows **OUTPUT_FILE: lab2-dirb.txt** and the same wordlist (`common.txt`, 4612 words).

![Screenshot 66 - Saved DIRB scan: root results](Lab2-Screenshots/66_step19-dirb-saved-scan-results-01.png)

**Screenshot 66 - Saved DIRB scan: root results.** The same root results appear again: `/cgi-bin/` (403), `/dav/`, `/index.php`, `/phpinfo.php`, `/phpMyAdmin/`, `/server-status` (403), `/test/` and `/twiki/`.

![Screenshot 67 - Saved DIRB scan: dav and phpMyAdmin](Lab2-Screenshots/67_step19-dirb-saved-scan-results-02.png)

**Screenshot 67 - Saved DIRB scan: dav and phpMyAdmin.** `/dav/` is flagged as listable, then DIRB scans phpMyAdmin and lists `calendar`, `changelog`, `ChangeLog`, `contrib/`, `docs`, `error`, `export` and `favicon.ico`.

![Screenshot 68 - Saved DIRB scan: phpMyAdmin libraries and files](Lab2-Screenshots/68_step19-dirb-saved-scan-results-03.png)

**Screenshot 68 - Saved DIRB scan: phpMyAdmin libraries and files.** More phpMyAdmin results: `import`, `index.php`, `js/`, `lang/`, `libraries/`, `license`, `main`, `navigation`, `phpinfo`, `phpinfo.php` (size 0) and `phpmyadmin`.

![Screenshot 69 - Saved DIRB scan: documentation and setup](Lab2-Screenshots/69_step19-dirb-saved-scan-results-04.png)

**Screenshot 69 - Saved DIRB scan: documentation and setup.** The list continues with `print`, `readme`, `README`, `robots` and `robots.txt`, then `scripts/`, `setup/` (with `sql`), `test/`, `themes/` and `TODO`.

![Screenshot 70 - Saved DIRB scan: webapp and twiki](Lab2-Screenshots/70_step19-dirb-saved-scan-results-05.png)

**Screenshot 70 - Saved DIRB scan: webapp and twiki.** `webapp` is found, `/test/` is listable, and TWiki results follow: `bin/`, `data` (403), `index`, `index.html` and `lib/license`.

![Screenshot 71 - Saved DIRB scan: twiki pub and listable directories](Lab2-Screenshots/71_step19-dirb-saved-scan-results-06.png)

**Screenshot 71 - Saved DIRB scan: twiki pub and listable directories.** `twiki/pub/readme` (200) and `twiki/pub/templates` (403) are found, with `phpMyAdmin/contrib/` and `js/` marked listable.

![Screenshot 72 - Saved DIRB scan: listable warnings](Lab2-Screenshots/72_step19-dirb-saved-scan-results-07.png)

**Screenshot 72 - Saved DIRB scan: listable warnings.** Listable warnings continue for `phpMyAdmin/themes/`, `twiki/bin/` and `twiki/lib/`.

![Screenshot 73 - Saved DIRB scan: twiki pub](Lab2-Screenshots/73_step19-dirb-saved-scan-results-08.png)

**Screenshot 73 - Saved DIRB scan: twiki pub.** The scan reaches `twiki/pub/`, the last of the main directories before it finishes.

![Screenshot 74 - Saved DIRB scan completed](Lab2-Screenshots/74_step19-dirb-saved-scan-complete-found-43.png)

**Screenshot 74 - Saved DIRB scan completed.** The scan ends with `phpMyAdmin/setup/lib/` and `twiki/pub/Main/` as listable, and the summary reads **DOWNLOADED: 23060 - FOUND: 43**, so the saved run found 43 accessible resources.


**Report:** After the DIRB scan, the results were saved to a text file to preserve the findings. The saved scan examined the target and identified **43** accessible resources and directories. Saving the output means the paths and HTTP responses can be reviewed later without re-running the scan and gives supporting evidence for the content-discovery phase.

---

## Part F: SQL Injection Concepts on Mutillidae (Steps 20-25)

### Step 20: Confirm connectivity and the new target address

**Objective:** confirm the network after the lab's addresses changed, before starting the Mutillidae tests.

**Commands used:** `ip addr` and `ping -c 4 10.234.230.36`


![Screenshot 75 - Kali network interface and ping](Lab2-Screenshots/75_step20-mutillidae-vm-ip-addr-and-ping.png)

**Screenshot 75 - Kali network interface and ping.** `ip addr` shows the Kali machine's `eth0` interface is **UP** with address **10.234.230.36/24** (assigned dynamically) on the new `10.234.230.0/24` network. `ping -c 4 10.234.230.36` pings that interface. The replies come back with very low times (about 0.04 ms), as expected for a ping to the machine's own address, which confirms the interface was working.

![Screenshot 76 - Ping statistics](Lab2-Screenshots/76_step20-mutillidae-ping-statistics.png)

**Screenshot 76 - Ping statistics.** The summary shows **4 packets transmitted, 4 received, 0% packet loss**, with average round-trip time 0.049 ms. The testing machine's network was working after the change of subnet, so Mutillidae could be tested at `10.234.230.91`.


### Step 21: Identify user-controlled inputs

**Objective:** find every place where a user can give the application data, because each one is a possible injection point.


![Screenshot 77 - Mutillidae home page](Lab2-Screenshots/77_step21-mutillidae-home.png)

**Screenshot 77 - Mutillidae home page.** Browsing to `http://10.234.230.91/mutillidae/` loads **Mutillidae: Born to be Hacked**, version **2.1.19**, with **Security Level 0 (Hosed)** (no protection) and hints disabled. The menu (Login/Register, Toggle Hints, Toggle Security, Reset DB and others) and the OWASP Top 10 menu on the left show where input is accepted.

![Screenshot 78 - Mutillidae login form with baseline values](Lab2-Screenshots/78_step21-mutillidae-login-page-baseline.png)

**Screenshot 78 - Mutillidae login form with baseline values.** The Login page (`index.php?page=login.php`) with a **Name** field containing `admin` and a masked **Password**. These are the *normal values* used to record how the application behaves before special characters are tried.

![Screenshot 79 - SQL error page from the login form (input-identification stage)](Lab2-Screenshots/79_step21-mutillidae-baseline-sql-error.png)

**Screenshot 79 - SQL error page from the login form (input-identification stage).** This is the SQL error page returned by the login form, titled **Error: Failure is always an option and this situation proves it**. It reveals the file (`/var/www/mutillidae/process-login-attempt.php`), the line number and the **diagnostic SQL query**, `SELECT * FROM accounts WHERE username=''' AND password='test'`. The extra quotation mark in the query shows this is the same response produced by the single-quote test in Step 23. Either way, the page shows how the query is built, which is dangerous information in a real application. The normal values for comparison are `admin` and `password`, as recorded in the table below.


**Report:** The Mutillidae application was examined to find where users can give input. User-controlled fields were found in the Login/Register form and other forms. These inputs supply data the application processes and can influence its behaviour, so they are targets for input-validation testing.

**Inputs identified**

| Page | Parameter | Method | Normal Value |
|---|---|---|---|
| Login/Register | username | POST | admin |
| Login/Register | password | POST | password |
| Login/Register | login form field | POST | user supplied |

### Step 23: Test a single quotation mark

**Objective:** see how the application handles a character that has special meaning in SQL.


![Screenshot 80 - Single quote entered in the Name field](Lab2-Screenshots/80_step23-mutillidae-single-quote-input.png)

**Screenshot 80 - Single quote entered in the Name field.** A single quotation mark (`'`) is typed in the **Name** field, with a short password, before pressing Login. The quote is the character that closes a string in SQL, so it is the usual first test for improper input handling.

![Screenshot 81 - SQL error returned for the single quote](Lab2-Screenshots/81_step23-mutillidae-single-quote-sql-error.png)

**Screenshot 81 - SQL error returned for the single quote.** The page returns **Error executing query: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version...** The diagnostic query shows `WHERE username=''' AND password='test'`, so the quote that was typed was placed straight into the SQL statement and broke its syntax. This is strong evidence that the input is **not sanitised or parameterised**. The page also shows PHP *Cannot modify header information* warnings, which further expose internal file paths.


**Report:** A single quote was entered in the username field to test how special SQL characters are handled. The response differed from the baseline, with a database syntax error. This indicates user input is being inserted into the SQL statement without adequate validation, and the response was recorded as evidence for the SQL injection analysis.

### Step 24: Compare true and false conditions

**Objective:** observe how the application reacts to logical conditions that are always true or always false.


![Screenshot 82 - True condition submitted](Lab2-Screenshots/82_step24-mutillidae-true-condition-sql-error.png)

**Screenshot 82 - True condition submitted.** The input is a quote followed by a condition that is always true (`1 OR '1' =1`). The response is again an SQL syntax error, and the diagnostic query shows exactly how the text was inserted: `username='' 1 OR '1' =1' AND password='test'`. The message says the error is *near '1 OR '1' =1' AND password='test''*.

![Screenshot 83 - False condition submitted](Lab2-Screenshots/83_step24-mutillidae-false-condition-sql-error.png)

**Screenshot 83 - False condition submitted.** The same test with an always-false condition (`1 OR '1' =2`). The result is also an SQL syntax error, and the diagnostic query is `username='' 1 OR '1' =2' AND password='test'`. Both tests got the same type of response, so no data difference could be seen, but both prove that typed text becomes part of the SQL query.


**Report:** True and false SQL conditions were submitted through the Mutillidae login form. In both cases the application returned SQL syntax errors and diagnostic information, including parts of the generated SQL query. The responses showed that user input was placed directly into the SQL statement without validation or parameterisation, and illustrated how SQL operators and user-supplied values influence the structure of database queries.

### Step 25: Burp inspection

**Objective:** review the request and response that produced the SQL errors.


![Screenshot 84 - Login error showing the database and table name](Lab2-Screenshots/84_step25-mutillidae-admin-login-error-details.png)

**Screenshot 84 - Login error showing the database and table name.** Logging in as `admin` with the password `test` returns **Error executing query: Table 'metasploit.accounts' doesn't exist**, with the diagnostic query `SELECT * FROM accounts WHERE username='admin' AND password='test'`. This leaks the **database name (`metasploit`)** and the table being queried, and shows the application's database had not been set up (the page itself asks *Did you setup/reset the DB?*). This is a clear example of information disclosure through verbose error messages.

![Screenshot 85 - Burp HTTP history is empty](Lab2-Screenshots/85_step25-burp-http-history-empty.png)

**Screenshot 85 - Burp HTTP history is empty.** The **Proxy > HTTP history** tab says **HTTP history is empty**, so Burp did not record these Mutillidae requests. This suggests the Mutillidae tests were sent from a browser that was not routed through Burp (for example Firefox). The login request is therefore analysed from the application's own responses, which are enough to show the input reaches the database.


**Report:** The SQL injection test requests were reviewed using the application's responses. The login form sent user-controlled `username` and `password` parameters in an HTTP POST request, and the resulting SQL error showed that the input reached the backend database query. This provided enough evidence for analysis.

---

## Part G: Automated SQL Injection Testing with SQLMap (Steps 26-29)

### Step 26: SQLMap syntax and installation

**Objective:** install SQLMap, a tool that automates SQL injection testing.

**Command used:** `sqlmap --version`


![Screenshot 86 - SQLMap not installed, install requested](Lab2-Screenshots/86_step26-sqlmap-install-command.png)

**Screenshot 86 - SQLMap not installed, install requested.** `sqlmap --version` returns **Command 'sqlmap' not found**. The install is accepted and the sudo password entered. The status shows `0% [Working]` while the package list is prepared.

![Screenshot 87 - SQLMap downloading](Lab2-Screenshots/87_step26-sqlmap-install-downloading.png)

**Screenshot 87 - SQLMap downloading.** The summary shows **1 package, 7,395 kB download** and the package `sqlmap 1.10.8-1` being fetched and unpacked.

![Screenshot 88 - SQLMap installed](Lab2-Screenshots/88_step26-sqlmap-install-complete.png)

**Screenshot 88 - SQLMap installed.** SQLMap is set up (`Setting up sqlmap (1.10.8-1)`) and the prompt returns without errors. SQLMap is now available.


**Report:** SQLMap was installed and verified on Kali Linux and prepared for automated SQL injection testing. It automates testing and validation of SQL injection vulnerabilities, and this prepared the environment for the remaining database assessment exercises.

### Step 27: SQLMap test

**Objective:** let SQLMap test a parameter automatically.

**Command used:** `sqlmap -u "http://10.234.230.91/mutillidae/index.php?page=login.php"`


![Screenshot 89 - SQLMap test started](Lab2-Screenshots/89_step27-sqlmap-test-start.png)

**Screenshot 89 - SQLMap test started.** The first attempt was missing its closing quotation mark, so the shell waited with a `dquote>` prompt; it was corrected in the second command. SQLMap **1.10.8** shows its legal disclaimer (use only with prior consent), then tests the connection. It asks whether to use the server's `PHPSESSID` cookie (answered `y`), checks for a WAF/IPS, and finds that the GET parameter **`page` appears to be dynamic**. The basic heuristic test says `page` *might not be injectable*, but its heuristics suggest the parameter might be open to **cross-site scripting** and **file inclusion**.

![Screenshot 90 - SQLMap running its injection techniques](Lab2-Screenshots/90_step27-sqlmap-test-running.png)

**Screenshot 90 - SQLMap running its injection techniques.** SQLMap tests the `page` parameter with many techniques: boolean-based blind, error-based tests written for MySQL, PostgreSQL, Microsoft SQL Server, Oracle and H2, generic inline queries, stacked queries and time-based blind tests. It then recommends reducing the number of requests and asks `[Y/n]`.

![Screenshot 91 - SQLMap result: parameter not injectable](Lab2-Screenshots/91_step27-sqlmap-test-result-not-injectable.png)

**Screenshot 91 - SQLMap result: parameter not injectable.** After answering `y` it runs the generic UNION test (1 to 10 columns). It then gives the conclusion: **GET parameter 'page' does not seem to be injectable** and **all tested parameters do not appear to be injectable**. SQLMap suggests trying higher `--level` and `--risk` values, the `--tamper` option, or `--random-agent` if a WAF is suspected, and ends at 10:17:21.


**Report:** SQLMap was run against Mutillidae to test the `page` parameter. It connected, confirmed the parameter was dynamic and tried boolean, error, time-based and UNION techniques. It then reported that the parameter did not appear injectable under the current testing conditions. The result shows automated testing is useful but its findings should be checked against manual analysis. Importantly, SQLMap was pointed at the `page` GET parameter, not the POST login fields that produced SQL errors in Step 23, so this result does not clear the login form.

### Step 28: List databases

**Objective:** try to list the available databases.

**Command used:** `sqlmap -u "http://10.234.230.91/mutillidae/index.php?page=login.php" --dbs`


![Screenshot 92 - SQLMap --dbs command started](Lab2-Screenshots/92_step28-sqlmap-dbs-command.png)

**Screenshot 92 - SQLMap --dbs command started.** The same URL is tested again with the `--dbs` option, which asks SQLMap to list databases if an injection point is found. It begins with the same cookie question, finds `page` dynamic again and begins the boolean-based tests.

![Screenshot 93 - SQLMap --dbs result](Lab2-Screenshots/93_step28-sqlmap-dbs-result-not-injectable.png)

**Screenshot 93 - SQLMap --dbs result.** SQLMap again runs the full set of techniques, then shows **GET parameter 'page' does not seem to be injectable** and the **CRITICAL** message that all tested parameters are not injectable (ending 10:21:06). Because no injection point was confirmed, **no database list** could be produced.


**Report:** SQLMap was run with `--dbs` to list databases on the target. It tested the `page` parameter but found no confirmed SQL injection, so database enumeration could not be done. This shows that enumeration depends on an exploitable injection point.

### Step 29: List tables

**Objective:** try to list the tables in a database.

**Command used:** `sqlmap -u "http://10.234.230.91/mutillidae/index.php?page=login.php" --tables`


![Screenshot 94 - SQLMap --tables command started](Lab2-Screenshots/94_step29-sqlmap-tables-command.png)

**Screenshot 94 - SQLMap --tables command started.** The `--tables` option is added to the same command. SQLMap repeats its checks (cookie question, dynamic parameter, heuristics) and starts the boolean-based tests (started 10:27:07).

![Screenshot 95 - SQLMap --tables result](Lab2-Screenshots/95_step29-sqlmap-tables-result-not-injectable.png)

**Screenshot 95 - SQLMap --tables result.** The run ends with the same outcome: the `page` parameter **does not seem to be injectable** and all tested parameters are not injectable (ending 10:27:26). Without an injection point no table names could be retrieved.


**Report:** SQLMap was run with `--tables` to list database tables. It tested the `page` parameter with several techniques but found no confirmed injectable parameter, so no table information could be retrieved. Table enumeration needs a successfully exploitable SQL injection first.

---

## Final Report

The laboratory demonstrated core web application security testing techniques in a controlled environment. Browser Developer Tools, Burp Suite, Nikto, DIRB and SQLMap were used to identify and analyse security-related behaviour and possible weaknesses. Testing showed the risks of weak input validation, permissive file upload handling and information disclosure through detailed application and server messages. The exercises reinforced secure coding practices, server-side validation, prepared statements and proper access controls.

### Key Findings

- HTTP requests and cookies can be inspected with browser tools and Burp Suite.
- File upload functionality needs strong validation.
- File extensions alone should not be trusted for upload validation.
- Client-supplied MIME types can be manipulated.
- Web-accessible upload directories may introduce security risks.
- Directory discovery tools can reveal hidden application resources.
- Unsanitised user input can lead to SQL injection vulnerabilities.
- Detailed database error messages expose sensitive information (query structure, file paths, database and table names).
- Automated tools such as SQLMap complement manual testing but only test what they are pointed at.

---

## Findings Summary

| # | Finding | Evidence (screenshots) | Risk | Fix |
|---|---|---|---|---|
| 1 | Outdated server software (Apache 2.2.8, PHP 5.2.4) disclosed in headers | 41, 42 | High | Update software; hide version banners |
| 2 | HTTP TRACE method enabled | 42 | Medium | Disable TRACE |
| 3 | Missing security headers (X-Content-Type-Options, Permissions-Policy; deprecated X-Frame-Options) | 43, 45 | Low-Medium | Add modern security headers |
| 4 | Directory indexing/listing on `/icons/`, `/doc/`, `/dav/`, `/test/` and phpMyAdmin folders | 41, 46, 54, 59-63 | Medium | Disable `Options Indexes` |
| 5 | `phpinfo.php` exposed | 47, 53 | Medium | Remove test scripts |
| 6 | phpMyAdmin, including `setup/`, reachable from the web | 47, 55-56, 61 | High | Restrict by IP/authentication; remove setup |
| 7 | Upload accepts `.txt` and `.log` files with no type restriction, and shows its storage path | 28, 32 | Medium | Allowlist file types and validate content |
| 8 | User input placed directly in SQL queries (login form) | 81-84 | High | Use prepared statements |
| 9 | Verbose SQL and PHP errors reveal query, file paths and database name | 79, 81-84 | Medium | Disable detailed errors in production |

---

## Defensive Recommendations

- Validate uploaded files on the server side.
- Use allowlists for approved file types.
- Store uploaded files outside the web root.
- Implement parameterised queries and prepared statements.
- Disable verbose database and PHP error messages.
- Apply least-privilege database permissions.
- Validate and sanitise all user input.
- Keep Apache, PHP and phpMyAdmin updated and remove unused default files and test scripts.
- Disable directory listing and restrict access to administrative tools.
- Regularly review web server configuration and application security controls.

---

## Student Questions

**1. What is the difference between GET and POST?**
GET sends data in the URL and is normally used to request information from a server. POST sends data in the body of the HTTP request and is normally used to submit forms or upload files. GET data is visible in the browser address bar, whereas POST data is not.

**2. What is a parameter?**
A parameter is a user-supplied value sent to a web application through a URL, form field, cookie or request body. It lets users give the application information that it processes. In this lab, `page` (in the URL) and `username`/`password` (in the login form) were parameters.

**3. Why should cookies be hidden in reports?**
Cookies often hold session identifiers and authentication data. If a value such as `PHPSESSID` is exposed, someone could hijack the session or access the account. Only cookie names should be documented.

**4. What is multipart/form-data?**
It is the HTTP content type used when uploading files through web forms. It lets files and form fields travel together in a single request, each in its own part.

**5. Why are extension and MIME type different?**
The extension is part of the file name (such as `.txt`), while the MIME type describes the format of the content (such as `text/plain`). A file can have one extension while the MIME type sent in the request says something else, because the client can change it.

**6. Why is trusting client Content-Type weak?**
The `Content-Type` header is supplied by the client and can be altered before it reaches the server. An attacker can make a harmful file look harmless, so the server must always validate the actual content itself.

**7. Why should uploads be stored outside an executable web directory?**
Storing uploads outside the web root (or where scripts cannot run) stops uploaded files from being executed by the web server, which prevents an attacker from uploading a malicious script and running it.

**8. What kind of information did Nikto report?**
Nikto reported the server and PHP versions (Apache 2.2.8, PHP 5.2.4) and that they are outdated, directory indexing on `/icons/` and `/doc/`, the active HTTP TRACE method, mod_negotiation/MultiViews, missing security headers, PHP Easter eggs, the exposed `/phpinfo.php` page, default files such as `/icons/README`, and phpMyAdmin documentation. These are observations to verify manually.

**9. What do 200, 301/302, 403 and 404 generally mean in DIRB output?**
- **200:** the resource exists and was accessed successfully.
- **301/302:** the resource redirects to another location (a similar redirect, 303, appeared for `phpMyAdmin/setup/config`).
- **403:** the resource exists but access is forbidden.
- **404:** the resource was not found.

**10. Why establish a baseline before SQL tests?**
A baseline records how the application behaves with normal input. Knowing the expected response makes it easier to spot changes caused by test inputs, such as the new SQL syntax errors compared with the normal login attempt.

**11. What happened with a single quote?**
The single quote is a special SQL character that closes a string. When it was entered in the username field, Mutillidae returned an SQL syntax error and showed the diagnostic query, which proved the input was being inserted directly into the SQL statement.

**12. How did the true and false conditions differ?**
In this lab both conditions produced the same kind of result: an SQL syntax error that echoed the typed text inside the query (`1 OR '1' =1` for the true condition and `1 OR '1' =2` for the false condition). No data was returned for either, so the difference could not be seen in the results. The value of the test was showing that typed operators become part of the query. In a fully injectable form, a true condition would normally return matching records and a false one would return none.

**13. What did SQLMap automate that you already observed manually?**
SQLMap automated the testing of a parameter with many injection techniques (boolean-based, error-based, time-based, stacked and UNION). In this lab it tested the `page` parameter and did not confirm an injection or retrieve databases or tables. The manual tests, in contrast, had already shown SQL errors in the login form, which SQLMap was not pointed at. This shows the two approaches should be used together.

**14. Why do prepared statements stop input from being interpreted as SQL syntax?**
Prepared statements send the SQL command and the user's data separately. The database treats user input only as data, never as SQL code, so extra characters such as quotes cannot change the query.

**15. Why is authorisation mandatory?**
Authorisation makes sure security testing is legal and ethical. Testing a system without permission can break laws, policies and organisational rules, however the test is done. All testing here stayed within the authorised ICDFA lab.
