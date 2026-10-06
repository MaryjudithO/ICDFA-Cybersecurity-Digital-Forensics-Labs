# ICDFA Lab 2: OPNsense Policy, Logging and Packet Analysis

## Overview

This laboratory focused on firewall policy testing, traffic filtering, logging analysis, state inspection, NAT verification, and packet capture correlation using OPNsense and Wireshark.

The exercise demonstrated how firewall rules affect network traffic and how blocked and permitted connections can be identified through firewall logs and packet captures.

All activities were conducted within an authorised and isolated laboratory environment using OPNsense as the perimeter firewall and Ubuntu as the client workstation.

---

## Student Information
 
- Name: Maryjudith Chidinma Ogunaka
- Registration Number: C11/26/FCDF/17151
- Programme: ICDFA Trainee | Cohort 11
- Module: WADF105 Network Security Fundamentals
- Lab Title: OPNsense Policy Logging and Packet Analysis


---

## Lab Environment

| Component | Description |
|------------|------------|
| Firewall | OPNsense Firewall |
| Client | Ubuntu Linux |
| LAN Network | 10.10.10.0/24 |
| Firewall LAN IP | 10.10.10.1 |
| Environment | Authorised Virtual Lab |

---

## Learning Objectives

- Understand firewall rule processing order.
- Create and test firewall block policies.
- Examine firewall log entries.
- Correlate firewall logs with packet captures.
- Analyse permitted and blocked traffic.
- Understand firewall states and outbound NAT.
- Restore normal network operation after testing.

---

## Activities Performed

### Part A – Baseline Verification

- Verified IP addressing
- Verified network routes
- Confirmed DNS functionality
- Confirmed HTTP and HTTPS connectivity

### Part B – Firewall Rule Review

- Examined LAN firewall rules
- Reviewed top-down rule processing

### Part C – ICMP Blocking

- Created an ICMP block rule
- Confirmed successful ICMP blocking
- Verified DNS and HTTPS remained operational

### Part D – HTTP Blocking

- Created an outbound HTTP block rule
- Verified HTTP traffic was blocked
- Confirmed HTTPS traffic remained permitted

### Part E – Firewall Log Analysis

- Examined OPNsense log entries
- Identified blocked traffic
- Verified matching firewall rules

### Part F – Wireshark Packet Analysis

- Captured blocked ICMP traffic
- Captured blocked HTTP traffic
- Captured permitted HTTPS traffic
- Analysed protocol behaviour

### Part G – State and NAT Analysis

- Examined firewall states
- Verified outbound NAT operation
- Identified translated connections

### Part H – Environment Restoration

- Disabled temporary firewall rules
- Confirmed connectivity restoration
- Returned lab environment to baseline state

---

## Tools Used

- OPNsense
- Ubuntu Linux
- Wireshark
- Curl
- Ping
- Firewall Logs

---

## Key Findings

- Firewall rules are processed from top to bottom.
- Specific block rules must be placed above allow rules.
- ICMP traffic can be blocked without affecting HTTPS.
- Blocked TCP connections produce retransmissions in Wireshark.
- HTTPS traffic completed a normal TCP handshake.
- Firewall logs clearly identify blocked connections.
- Outbound NAT translates private addresses for internet communication.
- Disabled rules successfully restored connectivity.

---

## Screenshots

- Figure 1: Baseline connectivity verification
- Figure 2: Firewall rules configuration
- Figure 3: ICMP blocking verification
- Figure 4: HTTP blocking verification
- Figure 5: Firewall log evidence
- Figure 6: Wireshark packet capture
- Figure 7: State table analysis
- Figure 8: Restored connectivity verification

---

## Conclusion

The laboratory successfully demonstrated how firewall policies influence network communications and how network administrators can use firewall logs, state tables, NAT information, and packet captures to understand and troubleshoot network behaviour. The exercise provided practical experience with OPNsense firewall administration and reinforced the importance of logging and packet analysis in network security operations.

---

## ⚠️ Authorisation and Ethical Use

This laboratory exercise was conducted as part of the ICDFA Cybersecurity and Digital Forensics Programme.

All testing was performed exclusively against authorised laboratory systems within an isolated training environment.

The techniques demonstrated within this repository should only be used on systems for which explicit permission has been granted.

---

## Author

Maryjudith Chidinma Ogunaka

ICDFA Trainee | Cohort 11
