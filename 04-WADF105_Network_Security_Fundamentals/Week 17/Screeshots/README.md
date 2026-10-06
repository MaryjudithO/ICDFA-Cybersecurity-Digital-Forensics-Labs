# WADF105 Lab - Screenshot Evidence

Virtual Lab Commissioning and Network Validation (Oracle VM VirtualBox, OPNsense + Ubuntu).

| # | File | What it shows |
|---|------|---------------|
| 01 | `01_All_VMs_Powered_Off.png` | VirtualBox Manager showing all 4 VMs powered off before configuration |
| 02 | `02_NAT_Network_Default_Name_Before_Rename.png` | NAT Networks tab with the default name NatNetwork (10.0.2.0/24, DHCP on) |
| 03 | `03_NAT_Network_Renamed_ICDFA-UPLINK.png` | NAT Network renamed to ICDFA-UPLINK (10.0.2.0/24, DHCP on) |
| 04 | `04_Client_Adapter1_ICDFA-LAN_MAC.png` | Ubuntu Client Adapter 1: Internal Network ICDFA-LAN, MAC generated |
| 05 | `05_Firewall_Adapter1_ICDFA-WAN_MAC.png` | OPNsense Firewall Adapter 1: Internal Network ICDFA-WAN, MAC generated |
| 06 | `06_Firewall_Adapter1_ICDFA-WAN_MAC_duplicate.png` | Duplicate of 05 (same settings and MAC) - safe to leave out of GitHub |
| 07 | `07_Firewall_Adapter2_ICDFA-LAN_MAC.png` | OPNsense Firewall Adapter 2: Internal Network ICDFA-LAN, MAC generated |
| 08 | `08_Firewall_Adapter3_ICDFA-DMZ_MAC.png` | OPNsense Firewall Adapter 3: Internal Network ICDFA-DMZ, MAC generated |
| 09 | `09_Firewall_Adapter4_Not_Attached.png` | OPNsense Firewall Adapter 4 disabled / not attached |
| 10 | `10_DMZ_Adapter1_ICDFA-DMZ_MAC.png` | Ubuntu DMZ Server Adapter 1: Internal Network ICDFA-DMZ, MAC generated |
| 11 | `11_DMZ_Adapter2_ICDFA-WAN_MAC.png` | Ubuntu DMZ Server Adapter 2: Internal Network ICDFA-WAN, MAC generated |
| 12 | `12_VRouter_Adapter1_NAT_ICDFA-UPLINK_MAC.png` | Ubuntu VRouter Adapter 1: NAT Network ICDFA-UPLINK, MAC generated |
| 13 | `13_VRouter_Adapter2_ICDFA-WAN_MAC.png` | Ubuntu VRouter Adapter 2: Internal Network ICDFA-WAN, MAC generated |
| 14 | `14_VRouter_Adapter3_Not_Attached.png` | Ubuntu VRouter Adapter 3 disabled / not attached |
| 15 | `15_VRouter_Adapter4_Not_Attached.png` | Ubuntu VRouter Adapter 4 disabled / not attached |
| 16 | `16_Client_IP_Address_Small_Window.png` | Client: ip -4 -br address only (10.10.10.188/24) - small text, 17 shows it clearer |
| 17 | `17_Client_IP_Address_and_Route.png` | Client: ip -4 -br address and ip route (default via 10.10.10.1) |
| 18 | `18_DMZ_IP_Address_and_Route.png` | DMZ Server: ip -4 -br address (10.10.20.10/24) and ip route |
| 19 | `19_VRouter_IP_Address_and_Route.png` | VRouter: login, ip -4 -br address (10.0.2.3 and 172.16.100.1/30) and ip route |
| 20 | `20_Firewall_Console_Interfaces.png` | OPNsense console: LAN 10.10.10.1/24, DMZ 10.10.20.1/24, WAN 172.16.100.2/30 |
| 21 | `21_Client_Ping_LAN_Gateway_10.10.10.1.png` | Client: IP, route, then ping -c 4 10.10.10.1 (0% loss) |
| 22 | `22_Client_Ping_LAN_and_DMZ_Gateways.png` | Client: ping 10.10.10.1 and ping 10.10.20.1 (0% loss) |
| 23 | `23_Client_Ping_DMZ_and_WAN_Interfaces.png` | Client: ping 10.10.20.1 and ping 172.16.100.2 (0% loss) |
| 24 | `24_VM_Snapshots_LAB-BASELINE-VALIDATED.png` | VirtualBox snapshots tab: LAB-BASELINE-VALIDATED on all 4 VMs |
| 25 | `25_Client_Internet_Ping_1.1.1.1_FAIL.png` | Client: ping 1.1.1.1 - 100% packet loss (no internet) |
| 26 | `26_Client_DNS_Test_getent_opnsense.org.png` | Client: getent hosts opnsense.org resolves (DNS works) |
| 27 | `27_Client_LAN_Gateway_Ping_Retest.png` | Client: ping -c 4 10.10.10.1 retest (0% loss), after DNS test |
| 28 | `28_Client_Web_Test_curl_FAIL.png` | Client: curl -I https://opnsense.org fails (port 443 timed out) |
| 29 | `29_Wireshark_Launch_Window.png` | Wireshark opened from the terminal (small window) |
| 30 | `30_Wireshark_Select_Interface_enp0s3.png` | Wireshark welcome screen with enp0s3 selected (full screen) |
| 31 | `31_Wireshark_Capture_Running.png` | Wireshark capturing on enp0s3 (DNS, TCP and ARP traffic) |
| 32 | `32_Wireshark_Capture_Stopped.png` | Wireshark capture stopped, 29 packets collected |
| 33 | `33_Wireshark_ARP_Filter.png` | Wireshark filter arp: ARP request/reply for 10.10.10.1 |
| 34 | `34_Wireshark_DNS_Filter.png` | Wireshark filter dns: DNS queries and responses |
| 35 | `35_Wireshark_ICMP_Filter_No_Packets.png` | Wireshark filter icmp applied, 0 packets displayed (before ping) |
| 36 | `36_Wireshark_ICMP_Filter_Live_Capture_Empty.png` | Wireshark live capture with icmp filter, no packets yet |
| 37 | `37_Wireshark_ICMP_Echo_Request_Reply.png` | Wireshark filter icmp: Echo request/reply 10.10.10.188 <-> 10.10.10.1 |
