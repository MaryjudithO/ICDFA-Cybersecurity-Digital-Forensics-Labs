# WADF105 Lab 02: TCP/IP, Ethernet, ARP and Wireshark Analysis

## Student Information

**Name:** Maryjudith Chidinma Ogunaka  
**Registration Number:** C11/26/FCDF/17151  
**Programme:** ICDFA Fellowship in Cybersecurity and Digital Forensics  
**Cohort:** 11  
**Course:** WADF105 Network Security Fundamentals

---

## Introduction

This laboratory focused on the practical analysis of network communications using Wireshark within a virtual network environment. The exercise involved examining Ethernet communication, Address Resolution Protocol (ARP), Internet Control Message Protocol (ICMP), Domain Name System (DNS), and Transmission Control Protocol (TCP) traffic.

The laboratory provided practical experience in packet capture, protocol analysis and network troubleshooting techniques commonly used in cybersecurity and digital forensics.

---

## Laboratory Environment

The practical environment consisted of:

- Ubuntu Client
- OPNsense Firewall

Wireshark was installed on the Ubuntu Client and used as the primary packet capture and analysis tool throughout the exercise.

---

## Laboratory Objectives

The objectives of this laboratory were to:

- Examine Ethernet frame communication.
- Analyse ARP requests and replies.
- Observe ICMP echo traffic.
- Investigate DNS name resolution.
- Analyse TCP connection establishment.
- Develop packet capture and analysis skills using Wireshark.
- Understand communication across the TCP/IP protocol stack.

---

## Activities Performed

### Baseline Verification

The following commands were used to verify the client network configuration:

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

### ARP Analysis

- Generated ARP traffic.
- Captured ARP Requests.
- Captured ARP Replies.
- Examined MAC address resolution.

### ICMP Analysis

- Generated ICMP Echo Requests and Echo Replies.
- Analysed packet flow between hosts.
- Verified network connectivity.

### DNS Analysis

- Generated DNS queries.
- Captured DNS responses.
- Examined domain name resolution.

### TCP Analysis

- Generated web traffic using HTTP/HTTPS requests.
- Captured TCP packets.
- Identified the TCP three-way handshake (SYN, SYN-ACK, ACK).

---

## Evidence Collected

The laboratory evidence included:

- Baseline verification screenshots
- ARP analysis screenshots
- ICMP analysis screenshots
- DNS query and response screenshots
- TCP handshake screenshots
- Wireshark packet capture evidence
- Exported packet capture file

---

## Packet Capture File

The complete packet capture was successfully exported and saved as:

```text
C11-26-FCDF-17151_Lab02_Evidence.pcapng
```

The capture file contains evidence of:

- ARP communications
- ICMP traffic
- DNS queries and responses
- TCP communication sessions

---

## Tools and Technologies

- Oracle VM VirtualBox
- Ubuntu Linux
- OPNsense Firewall
- Wireshark
- TCP/IP
- Ethernet
- ARP
- ICMP
- DNS
- TCP

---

## Learning Outcomes

Upon completion of this laboratory, the following skills were developed:

- Network packet capture and analysis
- Protocol troubleshooting
- Ethernet and ARP investigation
- DNS traffic analysis
- TCP handshake analysis
- Wireshark proficiency
- Network communication analysis

---

## Result

✅ Baseline network configuration verified

✅ ARP Requests and Replies analysed

✅ ICMP communication analysed

✅ DNS queries and responses analysed

✅ TCP three-way handshake analysed

✅ Packet capture evidence exported successfully

✅ Laboratory completed successfully


