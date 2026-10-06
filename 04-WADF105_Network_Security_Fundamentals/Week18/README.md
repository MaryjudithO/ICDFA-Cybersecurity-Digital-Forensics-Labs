# WADF105 Lab 02: TCP/IP, Ethernet, ARP and Wireshark Analysis

## Student Information

**Student Name:** Maryjudith Chidinma Ogunaka  
**Registration Number:** C11/26/FCDF/17151  
**Programme:** ICDFA Fellowship in Cybersecurity and Digital Forensics  
**Cohort:** 11  
**Course:** WADF105 Network Security Fundamentals

---

## Introduction

This laboratory focused on the practical analysis of network communications within a controlled virtual laboratory environment. Using Wireshark, various protocols were captured and analysed to understand how devices communicate across a TCP/IP network.

The exercise provided practical exposure to Ethernet addressing, Address Resolution Protocol (ARP), Internet Control Message Protocol (ICMP), Domain Name System (DNS) operations, and Transmission Control Protocol (TCP) communication.

---

## Laboratory Environment

The practical environment was hosted on Oracle VM VirtualBox and consisted of the following virtual machines:

- Ubuntu Client
- OPNsense Firewall
- Ubuntu DMZ Server
- Ubuntu VRouter

The laboratory was conducted using the validated baseline environment established during Lab 01.

---

## Laboratory Objectives

The objectives of this laboratory were to:

- Examine Ethernet frame communication.
- Investigate ARP requests and replies.
- Analyse IPv4 and MAC addressing relationships.
- Capture and analyse ICMP traffic.
- Observe DNS name resolution traffic.
- Analyse TCP three-way handshakes.
- Develop packet analysis skills using Wireshark.
- Improve troubleshooting and protocol investigation techniques.

---

## Activities Performed

### Baseline Verification

The following commands were executed to verify the initial network configuration:

```bash
ip -4 -br address
```

```bash
ip -br link
```

```bash
ip route
```

```bash
ip neigh show
```

```bash
groups
```

```bash
wireshark --version | head -n 1
```

---

### ARP Analysis

- Generated ARP traffic through network communication.
- Identified ARP Requests.
- Identified ARP Replies.
- Examined source and destination MAC addresses.
- Analysed how IPv4 addresses are mapped to MAC addresses.

---

### ICMP Analysis

- Generated ICMP Echo Requests and Echo Replies
