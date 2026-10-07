# OPNsense Firewall Policy Logging & Traffic Analysis


## Overview

Hands-on lab for **WADF105 – Network Security Fundamentals** (ICDFA Fellowship in Cybersecurity and Digital Forensics, Cohort 11). It covers firewall policy testing, traffic filtering and log review on **OPNsense**, using an Ubuntu client to generate and verify traffic. All activities were performed in an authorised, isolated virtual lab.

| | |
|---|---|
| **Name** | Maryjudith Chidinma Ogunaka |
| **Registration No.** | C11/26/FCDF/17151 |
| **Programme** | ICDFA Fellowship in Cybersecurity and Digital Forensics, Cohort 11 |
| **Module** | WADF105 Network Security Fundamentals |
| **Lab** | Lab 02 – OPNsense Policy Logging and Packet Analysis |


---

## Objectives

- Verify baseline addressing and connectivity (ICMP, DNS, HTTP, HTTPS) through the firewall
- Review the LAN firewall rule set and understand top-down rule processing
- Create and apply a targeted **ICMP block** rule (LAN → `1.1.1.1`)
- Confirm that unrelated services (DNS, HTTPS) are unaffected by a protocol-specific rule
- Create an **outbound HTTP (TCP/80) block** rule while keeping HTTPS (TCP/443) allowed
- Review traffic in **Firewall → Log Files → Live View** and relate entries to rules
- Disable the lab rules and restore the network to a known-good state

## Lab Environment

| Component | Details |
|---|---|
| Firewall | OPNsense 26.7 (`icdfa-nslab-firewall-v1.icdfa.test`) |
| Client | Ubuntu (`icdfa-nslab-client-v1`), `10.10.10.157/24` |
| Upstream | Ubuntu VRouter (gateway `172.16.100.1`) |
| LAN | `10.10.10.0/24`, firewall LAN IP `10.10.10.1` |
| WAN | `172.16.100.2/30` |

## Rules Implemented

| # | Description | Action | Proto | Source | Destination | Port |
|---|---|---|---|---|---|---|
| 1 | `LAB2 BLOCK ICMP TO 1.1.1.1` | Block | ICMP | LAN net | 1.1.1.1 | any |
| 2 | `LAB2 BLOCK OUTBOUND HTTP` | Block | TCP | LAN net | any | 80 (HTTP) |

## Test Commands

```bash
ip -4 -br address              # client addressing
ping -c 4 10.10.10.1           # reach firewall
ping -c 4 1.1.1.1              # ICMP to internet
nslookup example.com           # DNS
curl -I http://example.com     # HTTP
curl -I https://example.com    # HTTPS
```

## Tools Used

- OPNsense (web GUI and console)
- Ubuntu Linux client
- `ping`, `nslookup`, `getent`, `curl`
- OPNsense Firewall Log Files / Live View

## Results at a Glance

| Test | Expected | Observed |
|---|---|---|
| Baseline ICMP / DNS / HTTP / HTTPS | Pass | Pass |
| ICMP to 1.1.1.1 after rule applied | Blocked | 100% packet loss |
| DNS after ICMP rule | Unaffected | Resolves |
| HTTPS after ICMP rule | Unaffected | `HTTP/2 200` |
| HTTPS after HTTP rule | Unaffected | `HTTP/2 200` |
| Connectivity after rules disabled | Restored | Rules disabled |


## Repository Structure

```
.
├── README.md
├── REPORT.md
└── screenshots/
    ├── 00a_… – 02_…    Environment & client verification
    ├── 03_… – 06_…     Baseline tests
    ├── 07a_… – 08c_…   ICMP rule creation & testing
    ├── 09_… – 11_…     ICMP block verification, DNS/HTTPS checks
    ├── 12a_… – 14_…    HTTP rule creation & testing
    └── 15_… – 16_…     Live View & restoration
```

## Key Takeaways

- Firewall rules are **protocol- and port-specific**: blocking ICMP does not affect DNS or HTTPS.
- Firewall rules are evaluated **top to bottom**, so rule order matters.
- Rules take effect only after **Apply changes** is clicked in OPNsense.
- Blocking HTTP (TCP/80) while permitting HTTPS (TCP/443) shows port-level policy control.
- Enable **Log** on rules whose matches you need to see in Live View.
- Always **restore** lab rules at the end to leave the environment in a known state.

## Authorisation and Ethical Use

This lab was completed as part of the ICDFA Cybersecurity and Digital Forensics Programme. All testing was performed only against authorised systems in an isolated training environment. Use these techniques only on systems you have explicit permission to test.

## Author

**Maryjudith Chidinma Ogunaka**
ICDFA Fellowship in Cybersecurity and Digital Forensics | Cohort 11

