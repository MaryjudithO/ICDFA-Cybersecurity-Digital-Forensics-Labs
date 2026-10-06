# WADF105 Lab 01: Virtual Lab Commissioning and Network Validation

## Student Information

**Name:** Maryjudith Chidinma Ogunaka  
**Registration Number:** C11/26/FCDF/17151  
**Course:** WADF105 Network Security Fundamentals  
**Programme:** ICDFA Fellowship in Cybersecurity and Digital Forensics  
**Cohort:** 11

---

## Overview

This laboratory focused on commissioning and validating a virtual network security environment using Oracle VM VirtualBox. The exercise involved configuring virtual machines, validating network connectivity, verifying IP addressing and routing, conducting connectivity tests, analysing network traffic using Wireshark, and creating a known-good baseline snapshot for future laboratory activities.

---

## Virtual Machines Used

### Ubuntu Client
Used for IP address verification, route validation, connectivity testing, DNS testing, web connectivity testing, and Wireshark packet analysis.

### OPNsense Firewall
Used as the primary firewall and gateway device providing LAN, DMZ, and WAN connectivity, routing services, and network security controls.

### Ubuntu DMZ Server
Used to represent services hosted within the Demilitarized Zone (DMZ) and to validate network segmentation.

### Ubuntu VRouter
Used to provide routing functionality between network segments and support network validation activities.

---

## Laboratory Objectives

- Configure VirtualBox networking components.
- Generate unique MAC addresses for enabled network adapters.
- Verify IPv4 addressing and routing information.
- Validate LAN, DMZ, and WAN connectivity.
- Confirm firewall interface configuration.
- Perform network connectivity testing.
- Analyse ARP, ICMP, and DNS traffic using Wireshark.
- Create a validated baseline snapshot for future laboratories.

---

## Activities Performed

### Network Configuration

- Created and verified the required VirtualBox network segments.
- Configured virtual adapters for all systems.
- Generated unique MAC addresses for all enabled adapters.

### Address and Route Validation

Verified network configuration using:

```bash
ip -4 -br address
```

and

```bash
ip route
```

on the required virtual machines.

### Firewall Validation

Verified OPNsense WAN, LAN, and DMZ interface assignments and connectivity.

### Connectivity Testing

Performed connectivity testing between network devices and network segments using standard network diagnostic tools.

### DNS and Web Validation

Performed DNS resolution and web connectivity testing from the Ubuntu Client.

### Wireshark Analysis

Captured and analysed:

- ARP traffic
- ICMP traffic
- DNS traffic

using Wireshark packet captures.

---

## Baseline Snapshot

A validated baseline snapshot was created for all virtual machines using the following name:

```text
LAB-BASELINE-VALIDATED
```

This snapshot provides a stable recovery point for subsequent practical laboratories.

---

## Evidence Included

This repository contains:

- Laboratory Report
- Configuration Screenshots
- Firewall Validation Evidence
- IP Address Verification 

