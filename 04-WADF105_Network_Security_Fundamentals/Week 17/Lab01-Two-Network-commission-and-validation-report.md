# WADF105  lab 01:Virtual Lab Commissioning and Network Validation


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

<<img width="959" height="502" alt="01_All_VMs_Powered_Off" src="https://github.com/user-attachments/assets/9a97ccea-0644-448b-9469-c75b4a85d2b3" />


*Figure 1: VirtualBox Manager showing all four virtual machines powered off.*

**Observation:** ICDFA-NSLAB, ICDFA-NSLAB-VRouter, ICDFA-NSLAB-DMZ and ICDFA-NSLAB-Firewall each show Powered Off, so adapter changes could be made safely.

### 4.2 NAT Network

A NAT Network was needed for the uplink. The default network (NatNetwork) was renamed to ICDFA-UPLINK. Both states are shown below.

<img width="955" height="498" alt="02_NAT_Network_Default_Name_Before_Rename" src="https://github.com/user-attachments/assets/f25e5d37-0018-4d6b-9e56-e94b608b34a4" />

*Figure 2: NAT Networks tab with the default name NatNetwork (10.0.2.0/24, DHCP enabled).*

**Observation:** The network started with the default name and the 10.0.2.0/24 prefix.

<img width="959" height="491" alt="03_NAT_Network_Renamed_ICDFA-UPLINK" src="https://github.com/user-attachments/assets/311aac59-672a-452c-af5f-c58ca5fd58e4" />


*Figure 3: NAT Network renamed to ICDFA-UPLINK (10.0.2.0/24, DHCP enabled).*

**Observation:** The name now matches the lab specification; the prefix and DHCP setting were unchanged.

### 4.3 Adapter configuration and MAC addresses

Each adapter was attached to its network and a new MAC address was generated using the refresh button beside the MAC Address field, so no two adapters share an address.

#### Ubuntu Client

<img width="584" height="399" alt="04_Client_Adapter1_ICDFA-LAN_MAC" src="https://github.com/user-attachments/assets/6cd65668-dc51-4781-8fa8-e4aa35ecfaa7" />


*Figure 4: Ubuntu Client (ICDFA-NSLAB), Adapter 1: Internal Network ICDFA-LAN with a generated MAC address.*

**Observation:** The client has a single adapter on ICDFA-LAN.

#### OPNsense Firewall

<img width="577" height="377" alt="05_Firewall_Adapter1_ICDFA-WAN_MAC" src="https://github.com/user-attachments/assets/32c84792-4dda-4a01-9808-2b20875ab118" />


*Figure 5: Firewall Adapter 1: Internal Network ICDFA-WAN with a generated MAC address.*

**Observation:** Adapter 1 is the firewall WAN-side connection.

<img width="586" height="382" alt="06_Firewall_Adapter1_ICDFA-WAN_MAC_duplicate" src="https://github.com/user-attachments/assets/1563bcdf-140b-4ff3-9c37-805cb00d8dba" />


*Figure 6: Firewall Adapter 2: Internal Network ICDFA-LAN with a generated MAC address.*

**Observation:** Adapter 2 faces the client network.

<img width="569" height="388" alt="07_Firewall_Adapter2_ICDFA-LAN_MAC" src="https://github.com/user-attachments/assets/ee46543f-8ce4-4ea0-8ba0-61e20c4eb1ec" />


*Figure 7: Firewall Adapter 3: Internal Network ICDFA-DMZ with a generated MAC address.*

**Observation:** Adapter 3 faces the DMZ network.

<img width="585" height="390" alt="08_Firewall_Adapter3_ICDFA-DMZ_MAC" src="https://github.com/user-attachments/assets/e465de56-a57e-47a5-a006-73e579871308" />


*Figure 8: Firewall Adapter 4: disabled and not attached.*

**Observation:** Adapter 4 is not used, as planned.

#### Ubuntu DMZ Server

<img width="583" height="378" alt="09_Firewall_Adapter4_Not_Attached" src="https://github.com/user-attachments/assets/67685b6c-8fba-4a55-91b4-9bfe92916123" />


*Figure 9: DMZ Server Adapter 1: Internal Network ICDFA-DMZ with a generated MAC address.*

**Observation:** Adapter 1 connects the server to the DMZ.

<img width="590" height="391" alt="10_DMZ_Adapter1_ICDFA-DMZ_MAC" src="https://github.com/user-attachments/assets/d2d04369-fdc5-438a-b7ff-104d3a2ee7dc" />


*Figure 10: DMZ Server Adapter 2: Internal Network ICDFA-WAN with a generated MAC address.*

**Observation:** Adapter 2 connects the server to the WAN segment.

#### Ubuntu VRouter

<img width="588" height="382" alt="11_DMZ_Adapter2_ICDFA-WAN_MAC" src="https://github.com/user-attachments/assets/6c85085e-5d9a-4a82-98e3-7d5538d7a2f6" />


*Figure 11: VRouter Adapter 1: NAT Network ICDFA-UPLINK with a generated MAC address.*

**Observation:** This is the only adapter attached to the NAT Network, giving the lab its uplink.

<img width="581" height="398" alt="12_VRouter_Adapter1_NAT_ICDFA-UPLINK_MAC" src="https://github.com/user-attachments/assets/8f278d8c-38ec-47a4-887f-7aaba7f153e9" />


*Figure 12: VRouter Adapter 2: Internal Network ICDFA-WAN with a generated MAC address.*

**Observation:** Adapter 2 connects the VRouter to the same WAN segment as the firewall.

<img width="590" height="398" alt="13_VRouter_Adapter2_ICDFA-WAN_MAC" src="https://github.com/user-attachments/assets/18af7369-5d7f-48ec-b270-9b5191ad1f06" />


*Figure 13: VRouter Adapter 3: disabled and not attached.*

**Observation:** Unused adapter left disabled.

<img width="593" height="391" alt="14_VRouter_Adapter3_Not_Attached" src="https://github.com/user-attachments/assets/b38cc4e9-26e5-42df-8a8d-7283de8abaf5" />


*Figure 14: VRouter Adapter 4: disabled and not attached.*

**Observation:** Unused adapter left disabled.

### 4.4 System startup

The virtual machines were started in the recommended order: OPNsense Firewall, Ubuntu VRouter, Ubuntu DMZ Server, then Ubuntu Client. All systems booted successfully.

### 4.5 IP address and route verification

On each Ubuntu system the commands `ip -4 -br address` and `ip route` were run to confirm addressing and routing.

<img width="582" height="394" alt="15_VRouter_Adapter4_Not_Attached" src="https://github.com/user-attachments/assets/954e730d-befd-46ab-a9f9-17af2e0837a7" />


*Figure 15: Ubuntu Client: ip -4 -br address and ip route.*

**Observation:** The client holds 10.10.10.188/24 on enp0s3. The default route is via 10.10.10.1 (learned by DHCP), and 10.10.10.0/24 is directly connected.

<img width="921" height="435" alt="16_Client_IP_Address_Small_Window" src="https://github.com/user-attachments/assets/bdc51104-7c0e-46ab-bb4c-ad9333371a49" />


*Figure 16: Ubuntu DMZ Server: ip -4 -br address and ip route.*

**Observation:** The DMZ interface (enp0s8) holds 10.10.20.10/24, with the connected route 10.10.20.0/24

<img width="928" height="428" alt="17_Client_IP_Address_and_Route" src="https://github.com/user-attachments/assets/45cc89e3-ef44-4914-8659-20eab2a01348" />


*Figure 17: Ubuntu VRouter: login, ip -4 -br address and ip route.*

**Observation:** The VRouter has 10.0.2.3/24 on enp0s3 (NAT uplink) and 172.16.100.1/30 on enp0s8 (WAN segment). Its default route is via 10.0.2.1, and 172.16.100.0/30 is directly connected.

### 4.6 Firewall interface verification

<img width="644" height="407" alt="18_DMZ_IP_Address_and_Route" src="https://github.com/user-attachments/assets/246b5985-f9da-4048-9f7a-8e11cd0283f5" />


*Figure 18: OPNsense firewall console showing the interface assignments.*

**Observation:** LAN (em1) is 10.10.10.1/24, DMZ (em2) is 10.10.20.1/24 and WAN (em0) is 172.16.100.2/30. The WAN address sits in the same /30 as the VRouter (172.16.100.1), and the LAN and DMZ addresses are the gateways for the client and the DMZ server.

### 4.7 Connectivity testing from the Ubuntu Client

Three tests confirmed that the client could reach each firewall interface. Each used `ping -c 4`.

<img width="644" height="410" alt="19_VRouter_IP_Address_and_Route" src="https://github.com/user-attachments/assets/a1f37543-5e09-4783-b477-5f19892f165c" />

*Figure 19: Ubuntu Client: ping -c 4 10.10.10.1 (LAN gateway).*

**Observation:** 4 packets transmitted, 4 received, 0% packet loss, average round-trip about 3.4 ms. Result: **PASS**.

<img width="343" height="205" alt="20_Firewall_Console_Interfaces" src="https://github.com/user-attachments/assets/9f088ba3-01af-44e5-809e-5a5db32b502b" />

*Figure 20: Ubuntu Client: ping to the LAN gateway (10.10.10.1) and the DMZ interface (10.10.20.1).*

**Observation:** Both tests returned 4 of 4 replies with 0% packet loss; the DMZ interface averaged about 8.0 ms. Result: **PASS**.

<img width="926" height="428" alt="21_Client_Ping_LAN_Gateway_10 10 10 1" src="https://github.com/user-attachments/assets/7f0587ea-efa2-421e-aee3-b0585451cbc6" />


*Figure 21: Ubuntu Client: ping to the DMZ interface (10.10.20.1) and the WAN interface (172.16.100.2).*

**Observation:** Both returned 0% packet loss; the WAN interface averaged about 5.1 ms. This shows the client can reach all three firewall interfaces through its default gateway. Result: **PASS**.

### 4.8 Baseline snapshot

After the addressing and connectivity checks passed, a snapshot named LAB-BASELINE-VALIDATED was taken on each virtual machine to give a stable recovery point.

<img width="926" height="434" alt="22_Client_Ping_LAN_and_DMZ_Gateways" src="https://github.com/user-attachments/assets/063e4dda-2967-4849-8d96-b496d42de095" />


*Figure 22: VirtualBox Snapshots view showing LAB-BASELINE-VALIDATED on all four virtual machines.*

**Observation:** Each machine lists LAB-BASELINE-VALIDATED, taken on 29 September 2026 at 10:45 AM.

## 5. Lab 01: Internet, DNS and Web Testing

The second part of the laboratory tested external connectivity from the Ubuntu Client and then used Wireshark to inspect the traffic.

### 5.1 Internet reachability

<img width="926" height="436" alt="23_Client_Ping_DMZ_and_WAN_Interfaces" src="https://github.com/user-attachments/assets/c115d8c6-b559-4fa8-8c87-e93c256485c2" />


*Figure 23: Ubuntu Client: ping -c 4 1.1.1.1.*

**Observation:** 4 packets transmitted, 0 received, 100% packet loss. Internet connectivity was not available at the time of testing. Result: **FAIL**.

### 5.2 DNS resolution

<img width="959" height="503" alt="24_VM_Snapshots_LAB-BASELINE-VALIDATED" src="https://github.com/user-attachments/assets/8f4f9124-4c14-4bae-a23c-6570fb15d437" />


*Figure 24: Ubuntu Client: getent hosts opnsense.org.*

**Observation:** The name resolved to the IPv6 address 2001:1af8:2050:a001:1::1, so DNS resolution was working. Result: **PASS**.

### 5.3 Gateway retest

<img width="932" height="434" alt="25_Client_Internet_Ping_1 1 1 1_FAIL" src="https://github.com/user-attachments/assets/1c7633d2-6b7d-481e-9688-81347f882ce9" />


*Figure 25: Ubuntu Client: ping -c 4 10.10.10.1 after the DNS test.*

**Observation:** The LAN gateway still replied to all 4 packets with 0% packet loss, so the failure was not on the local LAN segment.

### 5.4 Web connectivity

<img width="923" height="433" alt="26_Client_DNS_Test_getent_opnsense org" src="https://github.com/user-attachments/assets/04ff7754-68b2-428a-bdde-0eddcfdb6374" />


*Figure 26: Ubuntu Client: curl -I https://opnsense.org.*

**Observation:** curl error 28: failed to connect to opnsense.org on port 443 after about 135 seconds. Web connectivity could not be established. Result: **FAIL**.

## 6. Wireshark Analysis

Wireshark 4.2.2 was opened on the Ubuntu Client and set to capture on the interface enp0s3. Display filters were then used to isolate each protocol.

<img width="920" height="422" alt="27_Client_LAN_Gateway_Ping_Retest" src="https://github.com/user-attachments/assets/9531b125-9116-4d37-8068-cbedc712e033" />


*Figure 27: Wireshark opened from the terminal on the Ubuntu Client.*

**Observation:** The application started and listed the available capture interfaces.

<img width="925" height="413" alt="28_Client_Web_Test_curl_FAIL" src="https://github.com/user-attachments/assets/f8dbcda2-3a03-4d12-9381-087640e7ee82" />


*Figure 28: Wireshark welcome screen with the enp0s3 interface selected.*

**Observation:** enp0s3 is the client adapter on ICDFA-LAN, so it carries all of the client traffic.

<img width="550" height="388" alt="29_Wireshark_Launch_Window" src="https://github.com/user-attachments/assets/52dc3fd1-4ea9-4e23-955a-9bd455f34a7e" />


*Figure 29: Wireshark capturing live on enp0s3.*

**Observation:** The capture shows DNS queries to 10.10.10.1, ARP traffic and repeated TCP SYN retransmissions to external addresses, which matches the failed internet and web tests.


<img width="955" height="442" alt="30_Wireshark_Select_Interface_enp0s3" src="https://github.com/user-attachments/assets/588ca0bb-a75a-433a-aa77-472ba4697d1b" />


*Figure 30: Wireshark capture stopped.*

**Observation:** The capture contained 29 packets and was kept for filtering.

### 6.1 ARP traffic

<img width="926" height="410" alt="31_Wireshark_Capture_Running" src="https://github.com/user-attachments/assets/ecd4c982-60f3-4327-94cd-e076ee7fa0c8" />


*Figure 31: Wireshark with the display filter arp.*

**Observation:** 4 of 29 packets displayed. The client (10.10.10.188) asks who has 10.10.10.1, and the gateway replies with its MAC address. This is how the client finds the gateway on its LAN. Result: **PASS**.

### 6.2 DNS traffic

<img width="925" height="419" alt="32_Wireshark_Capture_Stopped" src="https://github.com/user-attachments/assets/2bf3766c-fbda-4328-b6d6-53ed01cfa5a3" />

*Figure 32: Wireshark with the display filter dns.*

**Observation:** 8 of 29 packets displayed. The client sends DNS queries to 10.10.10.1 and receives responses, so the firewall is answering DNS for the client. Result: **PASS**.

### 6.3 ICMP traffic

The first ICMP capture attempt showed no packets because no ping had been sent while the capture was running. The ping was repeated during a fresh capture.

<img width="929" height="431" alt="33_Wireshark_ARP_Filter" src="https://github.com/user-attachments/assets/871a775a-f507-426b-b801-8600206524c7" />

*Figure 33: Wireshark with the display filter icmp applied before any ping traffic was captured.*

**Observation:** 0 packets displayed. This is why an additional capture was needed.

<img width="923" height="433" alt="34_Wireshark_DNS_Filter" src="https://github.com/user-attachments/assets/88608db6-63e2-420d-ad4c-e19807ce8c18" />


*Figure 34: Wireshark with the display filter icmp showing echo requests and replies.*

**Observation:** 8 ICMP packets displayed: four echo requests from 10.10.10.188 and four echo replies from 10.10.10.1 (sequence numbers 1 to 4, TTL 64). Result: **PASS**.

<img width="932" height="440" alt="35_Wireshark_ICMP_Filter_No_Packets" src="https://github.com/user-attachments/assets/5f7e7671-4784-4dff-b133-a623608c0071" />

<img width="929" height="431" alt="36_Wireshark_ICMP_Filter_Live_Capture_Empty" src="https://github.com/user-attachments/assets/fc775c26-a0f8-4ea4-9282-17b049c7ecd6" />

<img width="929" height="423" alt="37_Wireshark_ICMP_Echo_Request_Reply" src="https://github.com/user-attachments/assets/ae268262-6145-4571-b81e-cae88fd2a585" />


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

