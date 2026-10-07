# Lab 3: Bash Scripting - Build a Reconnaissance Tool

## Project Description

For this lab, I built a Bash script that automates three reconnaissance tools: WhatWeb, Nmap, and DIRB into a single menu-driven tool. The script prompts the user for an authorised target, validates the input, displays a menu, and runs the selected reconnaissance tool against the target.

## Features

- Accepts user-supplied target input
- No hardcoded target
- Input validation
- Menu-driven interface
- Uses Bash `if` statements
- Uses Bash `case` statements
- Executes WhatWeb
- Executes Nmap
- Executes DIRB
- Checks required tools before execution

## Tools Used

- Bash
- Nmap
- WhatWeb
- DIRB

## Usage

Make the script executable:

```bash
chmod +x recon_tool.sh
```

Run the script:

```bash
./recon_tool.sh
```

## Repository Structure

```text
Lab-3-Bash-Recon-Tool/
│
├── README.md
├── recon_tool.sh
├── ICDFA_Lab3_Bash_Reconnaissance_Tool_Report_Maryjudith_Ogunaka.pdf
└── screenshots/
    ├── figure1.png
    ├── figure2.png
    ├── figure3.png
    ├── figure4.png
    ├── figure5.png
    ├── figure6.png
    ├── figure7.png
    ├── figure8.png
    ├── figure9.png
    ├── figure10.png
    └── figure11.png
```

## Evidence Screenshots

- Figure 1 - Tool Verification
- Figure 2 - Script Header and Input Validation
- Figure 3 - Completed Case Statement
- Figure 4 - Executable Permission Verification
- Figure 5 - Connectivity Check
- Figure 6 - Script Execution and Menu Selection
- Figure 7 - Nmap Scan Results
- Figure 8 - WhatWeb Scan Results
- Figure 9 - DIRB Tool Startup
- Figure 10 - DIRB Directory Discovery Results
- Figure 11 - Script Backup and Verification

## ⚠️ Authorisation
This project was created for educational purposes as part of the ICDFA Cybersecurity and Digital Forensics Programme.

All testing was performed exclusively within an authorised laboratory environment against the designated Metasploitable 2 training target. The techniques and tools demonstrated in this repository should only be used on systems for which explicit permission has been granted.

Unauthorised scanning or testing of systems and networks may violate organisational policies, terms of service, and applicable laws.

