# WADF105 Lab 02: TCP/IP, Ethernet, ARP and Wireshark Analysis

## Student Information

**Student Name:** Maryjudith Chidinma Ogunaka  
**Registration Number:** C11/26/FCDF/17151  
**Programme:** ICDFA Fellowship in Cybersecurity and Digital Forensics  
**Cohort:** 11  
**Course:** WADF105 Network Security Fundamentals  
**Date:** 06 October 2026

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

The following commands were executed on the Ubuntu Client to verify the initial network configuration:

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

The OPNsense firewall console was also checked to confirm its interface addresses.

![Figure 1](screenshots/Lab02_01_Firewall_Console_Interfaces.png)

*Figure 1: OPNsense firewall console showing the interface addresses.*

**Observation:** The firewall LAN interface (em1) is 192.168.1.1/24 and the WAN interface (em0) received 10.0.2.15/24 by DHCP.

![Figure 2](screenshots/Lab02_02_WAN_and_Internet_Ping.png)

*Figure 2: Ubuntu Client: ping -c 4 10.0.2.2 and ping -c 4 1.1.1.1.*

**Observation:** Both tests returned 4 of 4 replies with 0% packet loss. The average round-trip time was about 3.6 ms for 10.0.2.2 and about 187.7 ms for 1.1.1.1.

![Figure 3](screenshots/Lab02_03_IP_Address_and_Route_Verification.png)

*Figure 3: Ubuntu Client: ip -4 -br address and ip route.*

**Observation:** The client holds 192.168.1.127/24 on interface enp0s3. The default route is via 192.168.1.1 (the firewall LAN address), and 192.168.1.0/24 is directly connected.

---

### ARP Analysis

- Generated ARP traffic through network communication.
- Identified ARP Requests.
- Identified ARP Replies.
- Examined source and destination MAC addresses.
- Analysed how IPv4 addresses are mapped to MAC addresses.


![Figure 4](screenshots/Lab02_04_ARP_Request_and_Reply.png)

*Figure 4: Wireshark with the display filter arp.*

**Observation:** 4 of 6750 packets were displayed. The client sent the request *Who has 192.168.1.1? Tell 192.168.1.127*, and the firewall answered *192.168.1.1 is at 08:00:27:c3:99:d5*. The request is a broadcast, so every device on the LAN receives it, while the reply is sent directly back to the client. This is how the client learned the MAC address of its gateway.

---

### ICMP Analysis

- Generated ICMP Echo Requests and Echo Replies using the `ping` command.
- Captured the packets in Wireshark and applied the `icmp` display filter.
- Examined the request and reply packets, including the identifier, sequence number and TTL.


![Figure 5](screenshots/Lab02_05_ICMP_Echo_Request_Reply.png)

*Figure 5: Wireshark with the display filter icmp.*

**Observation:** Echo Request and Echo Reply packets were captured between 192.168.1.127 and 192.168.1.1, with TTL 64 on the request. Each request is matched to its reply by the identifier and sequence number, and Wireshark links them ("reply in" / "request in").

---

### DNS Analysis

DNS traffic was generated with the following command:

```bash
getent hosts google.com
```

![Figure 6](screenshots/Lab02_06_DNS_Test_getent_google.com.png)

*Figure 6: Ubuntu Client: ping -c 4 1.1.1.1 and getent hosts google.com.*

**Observation:** The domain name google.com resolved to IPv6 addresses (2a00:1450:4009:c04::8b, ::8a, ::65 and ::66), so name resolution was working.

![Figure 7](screenshots/Lab02_07_DNS_Query_and_Response.png)

*Figure 7: Wireshark with the display filter dns.*

**Observation:** The client (192.168.1.127) sent a DNS query for google.com to the firewall (192.168.1.1) over UDP port 53, and the firewall returned a standard query response. The capture also shows queries for push.services.mozilla.com, which came from background browser activity.

---

### TCP Analysis

Web traffic was generated with the following command:

```bash
curl -I https://google.com
```

![Figure 8](screenshots/Lab02_08_TCP_Traffic_Generation_curl.png)

*Figure 8: Ubuntu Client: curl -I https://google.com.*

**Observation:** The server replied with HTTP/2 301 and a location header pointing to https://www.google.com/. This confirms the client could open an encrypted web connection.

![Figure 9](screenshots/Lab02_09_TCP_Traffic_Capture.png)

*Figure 9: Wireshark with the display filter tcp.*

**Observation:** The capture shows TCP connections from 192.168.1.127 to remote servers on port 443, with TLS application data, FIN/ACK packets for closing connections, and RST packets (shown in red) when connections were reset.

A TCP connection starts with a three-way handshake:

1. The client sends a **SYN** packet.
2. The server replies with a **SYN-ACK** packet.
3. The client sends an **ACK** packet and the connection is established.

<!-- After you retake the handshake screenshot (filter: tcp.flags.syn == 1), save it as screenshots/Lab02_09b_TCP_Three_Way_Handshake.png and remove the arrows on this line and the next line.
![Figure 9b](screenshots/Lab02_09b_TCP_Three_Way_Handshake.png)
-->
---

### Packet Capture Evidence

The capture was saved in PCAPNG format using **File > Save As** in Wireshark.

![Figure 10](screenshots/Lab02_10_Save_Capture_File_Dialog.png)

*Figure 10: Wireshark Save Capture File As window, with the file type set to pcapng.*

![Figure 11](screenshots/Lab02_11_PCAPNG_File_Saved.png)

*Figure 11: Wireshark showing the saved capture file in the title bar.*

**Observation:** The capture was saved as `C11-26-FCDF-17151_Lab02_Evidence.pcapng`. It contains ARP, ICMP, DNS and TCP traffic.

---

## Results Summary

| Test | Result |
|------|--------|
| IPv4 address, default gateway and route verification | PASS |
| ARP request and reply captured | PASS |
| ICMP echo request and reply captured | PASS |
| DNS query and response captured | PASS |
| TCP traffic to port 443 captured (HTTPS) | PASS |
| Capture saved as PCAPNG | PASS |

---

## Discussion

The laboratory showed how devices communicate in a TCP/IP network. ARP resolved the IPv4 address of the gateway into a MAC address so the client could deliver Ethernet frames on the local network. ICMP confirmed that hosts could reach each other, DNS translated a domain name into IP addresses, and TCP carried the encrypted web traffic. Using Wireshark display filters made it possible to separate each protocol from the rest of the traffic and examine it packet by packet.

---

## Conclusion

The objectives of the laboratory were achieved. Network traffic was generated, captured and analysed in Wireshark, and ARP, ICMP, DNS and TCP communication were all observed. The capture was saved as a PCAPNG file for evidence and future analysis.


