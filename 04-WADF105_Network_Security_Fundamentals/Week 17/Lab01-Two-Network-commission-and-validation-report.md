# WADF105  lab 02:Virtual Lab Commissioning and Network Validation


| Item | Details |
|------|---------|
| Student Name | Maryjudith Chidinma Ogunaka |
| Registration Number | C11FCDF26/17151 |
| Course | WADF105 Network Security Fundamentals |
| Laboratory | Virtual Lab Commissioning and Network Validation (Lab 01: Two-VM validation) |
| Platform | Oracle VM VirtualBox |
| Date | 06 October, 2026


## 1. Aim

The aim of this laboratory was to commission and validate the ICDFA Network Security virtual lab. This involved configuring the VirtualBox networks and adapters, generating unique MAC addresses, verifying IP addressing and routing, testing connectivity between network segments, analysing traffic with Wireshark, and saving a baseline snapshot for future practicals.

## 2. Objectives

- Configure the NAT Network and internal networks in VirtualBox.
- Attach every virtual machine to the correct networks and generate unique MAC addresses.
- Verify IPv4 addresses and routing tables on the Ubuntu Client, DMZ Server and VRouter.
- Verify the OPNsense firewall interface addresses.
- Test connectivity from the Ubuntu Client to the firewall interfaces, the internet, DNS and the web.
- Capture and analyse ARP, DNS and ICMP traffic in Wireshark.
- Create the LAB-BASELINE-VALIDATED snapshot on every virtual machine.


## 3. Lab Environment

### 3.1 Virtual machines

| VirtualBox name | Role | Operating system |
|-----------------|------|------------------|
| ICDFA-NSLAB | Ubuntu Client | Ubuntu Linux |
| ICDFA-NSLAB-Firewall | OPNsense Firewall | OPNsense 26.7 (amd64) |
| ICDFA-NSLAB-DMZ | Ubuntu DMZ Server | Ubuntu Linux |
| ICDFA-NSLAB-VRouter | Ubuntu VRouter | Ubuntu 24.04 LTS |


### 3.2 Networks

| Network | Type | Notes |
|---------|------|-------|
| ICDFA-UPLINK | NAT Network | IPv4 prefix 10.0.2.0/24, DHCP enabled |
| ICDFA-WAN | Internal Network | Links the Firewall, DMZ Server and VRouter |
| ICDFA-LAN | Internal Network | Links the Firewall and the Ubuntu Client |
| ICDFA-DMZ | Internal Network | Links the Firewall and the DMZ Server |

Internal network names in VirtualBox are case-sensitive, so each name was entered exactly as specified.


### 3.3 Adapter plan

| Virtual machine | Adapter 1 | Adapter 2 | Adapter 3 | Adapter 4 |
|-----------------|-----------|-----------|-----------|-----------|
| Ubuntu Client | ICDFA-LAN | Disabled | Disabled | Disabled |
| OPNsense Firewall | ICDFA-WAN | ICDFA-LAN | ICDFA-DMZ | Disabled |
| Ubuntu DMZ Server | ICDFA-DMZ | ICDFA-WAN | Disabled | Disabled |
| Ubuntu VRouter | ICDFA-UPLINK (NAT) | ICDFA-WAN | Disabled | Disabled |


## 4. Procedure and Evidence

### 4.1 Initial state

Before any configuration, VirtualBox Manager was checked to confirm that all four virtual machines were powered off.

![Figure 1](screenshots/01_All_VMs_Powered_Off.png)

*Figure 1: VirtualBox Manager showing all four virtual machines powered off.*

**Observation:** ICDFA-NSLAB, ICDFA-NSLAB-VRouter, ICDFA-NSLAB-DMZ and ICDFA-NSLAB-Firewall each show Powered Off, so adapter changes could be made safely.

### 4.2 NAT Network

A NAT Network was needed for the uplink. The default network (NatNetwork) was renamed to ICDFA-UPLINK. Both states are shown below.

![Figure 2](screenshots/02_NAT_Network_Default_Name_Before_Rename.png)

*Figure 2: NAT Networks tab with the default name NatNetwork (10.0.2.0/24, DHCP enabled).*

**Observation:** The network started with the default name and the 10.0.2.0/24 prefix.

![Figure 3](screenshots/03_NAT_Network_Renamed_ICDFA-UPLINK.png)

*Figure 3: NAT Network renamed to ICDFA-UPLINK (10.0.2.0/24, DHCP enabled).*

**Observation:** The name now matches the lab specification; the prefix and DHCP setting were unchanged.

### 4.3 Adapter configuration and MAC addresses

Each adapter was attached to its network and a new MAC address was generated using the refresh button beside the MAC Address field, so no two adapters share an address.

#### Ubuntu Client

![Figure 4](screenshots/04_Client_Adapter1_ICDFA-LAN_MAC.png)

*Figure 4: Ubuntu Client (ICDFA-NSLAB), Adapter 1: Internal Network ICDFA-LAN with a generated MAC address.*

**Observation:** The client has a single adapter on ICDFA-LAN.

#### OPNsense Firewall

![Figure 5](screenshots/05_Firewall_Adapter1_ICDFA-WAN_MAC.png)

*Figure 5: Firewall Adapter 1: Internal Network ICDFA-WAN with a generated MAC address.*

**Observation:** Adapter 1 is the firewall WAN-side connection.

![Figure 6](screenshots/07_Firewall_Adapter2_ICDFA-LAN_MAC.png)

*Figure 6: Firewall Adapter 2: Internal Network ICDFA-LAN with a generated MAC address.*

**Observation:** Adapter 2 faces the client network.

![Figure 7](screenshots/08_Firewall_Adapter3_ICDFA-DMZ_MAC.png)

*Figure 7: Firewall Adapter 3: Internal Network ICDFA-DMZ with a generated MAC address.*

**Observation:** Adapter 3 faces the DMZ network.

![Figure 8](screenshots/09_Firewall_Adapter4_Not_Attached.png)

*Figure 8: Firewall Adapter 4: disabled and not attached.*

**Observation:** Adapter 4 is not used, as planned.

#### Ubuntu DMZ Server

![Figure 9](screenshots/10_DMZ_Adapter1_ICDFA-DMZ_MAC.png)

*Figure 9: DMZ Server Adapter 1: Internal Network ICDFA-DMZ with a generated MAC address.*

**Observation:** Adapter 1 connects the server to the DMZ.

![Figure 10](screenshots/11_DMZ_Adapter2_ICDFA-WAN_MAC.png)

*Figure 10: DMZ Server Adapter 2: Internal Network ICDFA-WAN with a generated MAC address.*

**Observation:** Adapter 2 connects the server to the WAN segment.

#### Ubuntu VRouter

![Figure 11](screenshots/12_VRouter_Adapter1_NAT_ICDFA-UPLINK_MAC.png)

*Figure 11: VRouter Adapter 1: NAT Network ICDFA-UPLINK with a generated MAC address.*

**Observation:** This is the only adapter attached to the NAT Network, giving the lab its uplink.

![Figure 12](screenshots/13_VRouter_Adapter2_ICDFA-WAN_MAC.png)

*Figure 12: VRouter Adapter 2: Internal Network ICDFA-WAN with a generated MAC address.*

**Observation:** Adapter 2 connects the VRouter to the same WAN segment as the firewall.

![Figure 13](screenshots/14_VRouter_Adapter3_Not_Attached.png)

*Figure 13: VRouter Adapter 3: disabled and not attached.*

**Observation:** Unused adapter left disabled.

![Figure 14](screenshots/15_VRouter_Adapter4_Not_Attached.png)

*Figure 14: VRouter Adapter 4: disabled and not attached.*

**Observation:** Unused adapter left disabled.

### 4.4 System startup

The virtual machines were started in the recommended order: OPNsense Firewall, Ubuntu VRouter, Ubuntu DMZ Server, then Ubuntu Client. All systems booted successfully.

### 4.5 IP address and route verification

On each Ubuntu system the commands `ip -4 -br address` and `ip route` were run to confirm addressing and routing.

![Figure 15](screenshots/17_Client_IP_Address_and_Route.png)

*Figure 15: Ubuntu Client: ip -4 -br address and ip route.*

**Observation:** The client holds 10.10.10.188/24 on enp0s3. The default route is via 10.10.10.1 (learned by DHCP), and 10.10.10.0/24 is directly connected.

![Figure 16](screenshots/18_DMZ_IP_Address_and_Route.png)

*Figure 16: Ubuntu DMZ Server: ip -4 -br address and ip route.*

**Observation:** The DMZ interface (enp0s8) holds 10.10.20.10/24, with the connected route 10.10.20.0/24.

![Figure 17](screenshots/19_VRouter_IP_Address_and_Route.png)

*Figure 17: Ubuntu VRouter: login, ip -4 -br address and ip route.*

**Observation:** The VRouter has 10.0.2.3/24 on enp0s3 (NAT uplink) and 172.16.100.1/30 on enp0s8 (WAN segment). Its default route is via 10.0.2.1, and 172.16.100.0/30 is directly connected.

### 4.6 Firewall interface verification

![Figure 18](screenshots/20_Firewall_Console_Interfaces.png)

*Figure 18: OPNsense firewall console showing the interface assignments.*

**Observation:** LAN (em1) is 10.10.10.1/24, DMZ (em2) is 10.10.20.1/24 and WAN (em0) is 172.16.100.2/30. The WAN address sits in the same /30 as the VRouter (172.16.100.1), and the LAN and DMZ addresses are the gateways for the client and the DMZ server.

### 4.7 Connectivity testing from the Ubuntu Client

Three tests confirmed that the client could reach each firewall interface. Each used `ping -c 4`.

![Figure 19](screenshots/21_Client_Ping_LAN_Gateway_10.10.10.1.png)

*Figure 19: Ubuntu Client: ping -c 4 10.10.10.1 (LAN gateway).*

**Observation:** 4 packets transmitted, 4 received, 0% packet loss, average round-trip about 3.4 ms. Result: **PASS**.

![Figure 20](screenshots/22_Client_Ping_LAN_and_DMZ_Gateways.png)

*Figure 20: Ubuntu Client: ping to the LAN gateway (10.10.10.1) and the DMZ interface (10.10.20.1).*

**Observation:** Both tests returned 4 of 4 replies with 0% packet loss; the DMZ interface averaged about 8.0 ms. Result: **PASS**.

![Figure 21](screenshots/23_Client_Ping_DMZ_and_WAN_Interfaces.png)

*Figure 21: Ubuntu Client: ping to the DMZ interface (10.10.20.1) and the WAN interface (172.16.100.2).*

**Observation:** Both returned 0% packet loss; the WAN interface averaged about 5.1 ms. This shows the client can reach all three firewall interfaces through its default gateway. Result: **PASS**.

### 4.8 Baseline snapshot

After the addressing and connectivity checks passed, a snapshot named LAB-BASELINE-VALIDATED was taken on each virtual machine to give a stable recovery point.

![Figure 22](screenshots/24_VM_Snapshots_LAB-BASELINE-VALIDATED.png)

*Figure 22: VirtualBox Snapshots view showing LAB-BASELINE-VALIDATED on all four virtual machines.*

**Observation:** Each machine lists LAB-BASELINE-VALIDATED, taken on 29 September 2026 at 10:45 AM.

## 5. Lab 01: Internet, DNS and Web Testing

The second part of the laboratory tested external connectivity from the Ubuntu Client and then used Wireshark to inspect the traffic.

### 5.1 Internet reachability

![Figure 23](screenshots/25_Client_Internet_Ping_1.1.1.1_FAIL.png)

*Figure 23: Ubuntu Client: ping -c 4 1.1.1.1.*

**Observation:** 4 packets transmitted, 0 received, 100% packet loss. Internet connectivity was not available at the time of testing. Result: **FAIL**.

### 5.2 DNS resolution

![Figure 24](screenshots/26_Client_DNS_Test_getent_opnsense.org.png)

*Figure 24: Ubuntu Client: getent hosts opnsense.org.*

**Observation:** The name resolved to the IPv6 address 2001:1af8:2050:a001:1::1, so DNS resolution was working. Result: **PASS**.

### 5.3 Gateway retest

![Figure 25](screenshots/27_Client_LAN_Gateway_Ping_Retest.png)

*Figure 25: Ubuntu Client: ping -c 4 10.10.10.1 after the DNS test.*

**Observation:** The LAN gateway still replied to all 4 packets with 0% packet loss, so the failure was not on the local LAN segment.

### 5.4 Web connectivity

![Figure 26](screenshots/28_Client_Web_Test_curl_FAIL.png)

*Figure 26: Ubuntu Client: curl -I https://opnsense.org.*

**Observation:** curl error 28: failed to connect to opnsense.org on port 443 after about 135 seconds. Web connectivity could not be established. Result: **FAIL**.

## 6. Wireshark Analysis

Wireshark 4.2.2 was opened on the Ubuntu Client and set to capture on the interface enp0s3. Display filters were then used to isolate each protocol.

![Figure 27](screenshots/29_Wireshark_Launch_Window.png)

*Figure 27: Wireshark opened from the terminal on the Ubuntu Client.*

**Observation:** The application started and listed the available capture interfaces.

![Figure 28](screenshots/30_Wireshark_Select_Interface_enp0s3.png)

*Figure 28: Wireshark welcome screen with the enp0s3 interface selected.*

**Observation:** enp0s3 is the client adapter on ICDFA-LAN, so it carries all of the client traffic.

![Figure 29](screenshots/31_Wireshark_Capture_Running.png)

*Figure 29: Wireshark capturing live on enp0s3.*

**Observation:** The capture shows DNS queries to 10.10.10.1, ARP traffic and repeated TCP SYN retransmissions to external addresses, which matches the failed internet and web tests.

![Figure 30](screenshots/32_Wireshark_Capture_Stopped.png)

*Figure 30: Wireshark capture stopped.*

**Observation:** The capture contained 29 packets and was kept for filtering.

### 6.1 ARP traffic

![Figure 31](screenshots/33_Wireshark_ARP_Filter.png)

*Figure 31: Wireshark with the display filter arp.*

**Observation:** 4 of 29 packets displayed. The client (10.10.10.188) asks who has 10.10.10.1, and the gateway replies with its MAC address. This is how the client finds the gateway on its LAN. Result: **PASS**.

### 6.2 DNS traffic

![Figure 32](screenshots/34_Wireshark_DNS_Filter.png)

*Figure 32: Wireshark with the display filter dns.*

**Observation:** 8 of 29 packets displayed. The client sends DNS queries to 10.10.10.1 and receives responses, so the firewall is answering DNS for the client. Result: **PASS**.

### 6.3 ICMP traffic

The first ICMP capture attempt showed no packets because no ping had been sent while the capture was running. The ping was repeated during a fresh capture.

![Figure 33](screenshots/35_Wireshark_ICMP_Filter_No_Packets.png)

*Figure 33: Wireshark with the display filter icmp applied before any ping traffic was captured.*

**Observation:** 0 packets displayed. This is why an additional capture was needed.

![Figure 34](screenshots/37_Wireshark_ICMP_Echo_Request_Reply.png)

*Figure 34: Wireshark with the display filter icmp showing echo requests and replies.*

**Observation:** 8 ICMP packets displayed: four echo requests from 10.10.10.188 and four echo replies from 10.10.10.1 (sequence numbers 1 to 4, TTL 64). Result: **PASS**.

## 7. Default Gateway Explanation

The Ubuntu Client uses 10.10.10.1 as its default gateway because that is the LAN interface of the OPNsense firewall, and it is the only device on the client network that can forward traffic to other networks. Any destination outside 10.10.10.0/24 is therefore sent to the firewall first, as the route table in Figure 15 shows.

## 8. Troubleshooting and Challenges

- Network names and adapter assignments were reviewed and corrected where needed, and unique MAC addresses were generated for every enabled adapter.
- VirtualBox showed an "Invalid settings detected" warning at the bottom of the Firewall settings window (visible in Figures 5 to 8). The firewall still started, and its interfaces came up correctly (Figure 18).
- The internet ping and the web test failed. The local LAN, DMZ and WAN interface tests all passed, so the problem lies beyond the client-to-firewall path. It was not investigated further in this exercise.
- DNS testing needed a corrected command; `getent hosts opnsense.org` returned a result.
- The first ICMP capture showed no packets, so another capture was run while the ping was repeated.

**Suggested next step:** check forwarding, NAT and routing on the firewall WAN interface and the VRouter path to find why external traffic does not return.


## 9. Results Summary

| Test | Result |
|------|--------|
| IPv4 address and route verification (Client, DMZ Server, VRouter) | PASS |
| Firewall interface assignment | PASS |
| Ping LAN gateway 10.10.10.1 | PASS |
| Ping DMZ interface 10.10.20.1 | PASS |
| Ping WAN interface 172.16.100.2 | PASS |
| Internet reachability (ping 1.1.1.1) | FAIL |
| DNS resolution (getent hosts opnsense.org) | PASS |
| Web connectivity (curl -I https://opnsense.org) | FAIL |
| Wireshark ARP, DNS and ICMP captures | PASS |
| Baseline snapshot LAB-BASELINE-VALIDATED | PASS |


## 10. Conclusion

The ICDFA virtual lab was commissioned successfully. All virtual machines were attached to the correct networks with unique MAC addresses, the IP addressing and routing were verified, and the Ubuntu Client could reach the LAN, DMZ and WAN interfaces of the OPNsense firewall. Wireshark confirmed ARP, DNS and ICMP traffic between the client and the firewall, and the LAB-BASELINE-VALIDATED snapshot was saved on every machine. External internet and web connectivity were not achieved during testing, and this remains to be investigated in a later practical.

