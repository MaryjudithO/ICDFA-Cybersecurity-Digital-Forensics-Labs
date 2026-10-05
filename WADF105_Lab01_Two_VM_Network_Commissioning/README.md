# WADF105 Lab 01: Two-VM Network Commissioning and Validation

## Student Information

**Name:** Maryjudith Chidinma Ogunaka  
**Course:** WADF105 Network Security Fundamentals  
**Programme:** ICDFA Fellowship in Cybersecurity and Digital Forensics  
**Cohort:** Cohort 11

---

## Overview

This laboratory focused on commissioning, validating and documenting a two-virtual-machine network environment using Oracle VM VirtualBox. The environment consisted of an Ubuntu Client and an OPNsense Firewall configured to communicate across an isolated virtual network.

The exercise involved network adapter configuration, MAC address verification, IP addressing validation, routing verification, connectivity testing, packet analysis using Wireshark and creation of a validated baseline snapshot for future laboratories.

---

## Laboratory Objectives

- Configure the Ubuntu Client and OPNsense Firewall virtual machines.
- Generate unique MAC addresses for enabled network adapters.
- Verify IPv4 addressing and routing configuration.
- Validate firewall interface assignments.
- Perform connectivity and DNS testing.
- Capture and analyse ARP, ICMP and DNS traffic using Wireshark.
- Create a validated baseline snapshot for future practical exercises.

---

## Virtual Machines Used

- Ubuntu Client
- OPNsense Firewall

---

## Activities Performed

### Network Configuration
- Configured virtual network adapters.
- Connected systems to the required virtual network.
- Generated and recorded unique MAC addresses.

### Network Validation
- Verified IPv4 addressing using:

```bash
ip -4 -br address
