# Lab 02 Report: OPNsense Policy Logging and Packet Analysis

**Course:** WADF105 – Network Security Fundamentals
**Programme:** ICDFA Fellowship in Cybersecurity and Digital Forensics (Cohort 11)
**Student:** Maryjudith Chidinma Ogunaka
**Registration No.:** C11/26/FCDF/17151
**Date:** October 2026

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Aim](#2-aim)
3. [Laboratory Environment](#3-laboratory-environment)
4. [Procedure](#4-procedure)
5. [Results](#5-results)
6. [Analysis Questions](#6-analysis-questions)
7. [Observations and Limitations](#7-observations-and-limitations)
8. [Conclusion](#8-conclusion)
9. [Evidence Index](#9-evidence-index)

---

## 1. Introduction

This laboratory covers the implementation, testing and review of firewall security policies on an **OPNsense** firewall. Rules were created to block specific traffic, their effect was validated from a Linux client, and firewall logging was reviewed to understand how policy enforcement affects network communications.

## 2. Aim

To implement and verify firewall policies in OPNsense, observe their effect on network traffic, review firewall log information, and understand how policy enforcement influences communications.

## 3. Laboratory Environment

| Component | Details |
|---|---|
| Firewall | OPNsense 26.7 (amd64), `icdfa-nslab-firewall-v1.icdfa.test` |
| Client | Ubuntu, `icdfa-nslab-client-v1`, `10.10.10.157/24` |
| Router | Ubuntu VRouter (WAN gateway `172.16.100.1`) |
| LAN | `10.10.10.0/24` (firewall `10.10.10.1`) |
| WAN | `172.16.100.2/30` |

OPNsense acted as the gateway and security device; the Ubuntu client generated test traffic.

### Environment verification

**Firewall console (before restore)**

<img width="368" height="205" alt="00a_firewall_console_before_restore" src="https://github.com/user-attachments/assets/4860870b-f119-4296-8266-b8d5a7e1fa55" />

*Figure: `00a_firewall_console_before_restore.png`*

**Firewall LAN/WAN restored**

<img width="362" height="214" alt="00b_firewall_lan_wan_restored" src="https://github.com/user-attachments/assets/0954048a-634b-4763-b0f9-11f04b65bdee" />

*Figure: `00b_firewall_lan_wan_restored.png`*

**Client IP**

<img width="926" height="426" alt="01_client_ip_verification" src="https://github.com/user-attachments/assets/4716e604-283f-4d47-a7b0-3863e31e3e5a" />


*Figure: `01_client_ip_verification.png`*

**Client → Firewall ping**

<img width="925" height="429" alt="02_client_to_firewall_ping" src="https://github.com/user-attachments/assets/3939be2e-39a6-491e-9ca9-42f8c90504cb" />


*Figure: `02_client_to_firewall_ping.png`*

## 4. Procedure

1. Verified client addressing and reachability of the firewall.
2. Established baseline for ICMP, DNS, HTTP and HTTPS.
3. Created the rule `LAB2 BLOCK ICMP TO 1.1.1.1`.
4. Applied changes and tested ICMP.
5. Verified DNS and HTTPS were unaffected.
6. Created the rule `LAB2 BLOCK OUTBOUND HTTP` (TCP/80).
7. Tested HTTP and HTTPS.
8. Reviewed **Firewall → Log Files → Live View**.
9. Disabled both lab rules to restore connectivity.

## 5. Results

### 5.1 Baseline connectivity

| Test | Command | Result | Status |
|---|---|---|---|
| ICMP | `ping -c 4 1.1.1.1` | 4/4 received, 0% loss | PASS |
| DNS | `getent hosts example.com` | Resolved (IPv6 records shown) | PASS |
| HTTP | `curl -I http://example.com` | `HTTP/1.1 200 OK` | PASS |
| HTTPS | `curl -I https://example.com` | `HTTP/2 200` | PASS |

**ICMP**

<img width="926" height="401" alt="03_baseline_icmp" src="https://github.com/user-attachments/assets/d4fe1341-1a05-404f-ad42-31b71b6338de" />


*Figure: `03_baseline_icmp.png`*

**DNS**

![04_baseline_dns](screenshots/04_baseline_dns.png)

<img width="930" height="430" alt="04_baseline_dns" src="https://github.com/user-attachments/assets/f70823f4-bf4c-4569-9105-768735338be6" />

*Figure: `04_baseline_dns.png`*

**HTTP**

<img width="927" height="432" alt="05_baseline_http" src="https://github.com/user-attachments/assets/2d73efc7-cfef-4151-9ff7-fdb1de0ccbaf" />


*Figure: `05_baseline_http.png`*


**HTTPS**

<img width="930" height="430" alt="06_baseline_https" src="https://github.com/user-attachments/assets/8706b80c-c66d-4d27-aed5-50bb6f66cead" />


*Figure: `06_baseline_https.png`*

### 5.2 ICMP block rule

Rule `LAB2 BLOCK ICMP TO 1.1.1.1`: **Action** Block, **Interface** LAN, **Direction** In, **Protocol** ICMP, **Source** LAN network, **Destination** `1.1.1.1`, **Log** enabled.

**Login**

<img width="928" height="440" alt="07a_opnsense_login" src="https://github.com/user-attachments/assets/1390475e-100d-4b23-9ed8-5dcbddeb18b5" />

*Figure: `07a_opnsense_login.png`*

**Dashboard**

<img width="925" height="425" alt="07b_opnsense_dashboard" src="https://github.com/user-attachments/assets/0a9cee1a-feb7-4c9f-8048-46d3ed9e736b" />

*Figure: `07b_opnsense_dashboard.png`*

**Default LAN rules**

<img width="925" height="425" alt="07c_firewall_rules_default" src="https://github.com/user-attachments/assets/08c0c21e-7d4d-4224-adeb-657325cbefcb" />


*Figure: `07c_firewall_rules_default.png`*


**Rule: organisation**

<img width="455" height="314" alt="07d_icmp_rule_organisation" src="https://github.com/user-attachments/assets/9e2e287d-428b-4fe4-9dec-28ea376e6675" />


*Figure: `07d_icmp_rule_organisation.png`*

**Rule: filter**

<img width="446" height="314" alt="07e_icmp_rule_filter" src="https://github.com/user-attachments/assets/c20a80d3-79e0-4fbc-9cf2-808e8599c624" />


*Figure: `07e_icmp_rule_filter.png`*

**Rule: destination lookup**

<img width="452" height="311" alt="07f_icmp_rule_destination_lookup" src="https://github.com/user-attachments/assets/8ba1e8d0-a519-4ac3-9067-3e554641a716" />


<img width="452" height="311" alt="07f_icmp_rule_destination_lookup" src="https://github.com/user-attachments/assets/1609cc18-e1b9-4c57-b2e2-180320228230" />

*Figure: `07f_icmp_rule_destination_lookup.png`*

**Rule: destination set**


<img width="455" height="314" alt="08b_icmp_rule_destination_set" src="https://github.com/user-attachments/assets/2df3123c-de66-464d-966a-29947e52b332" />

*Figure: `08b_icmp_rule_destination_set.png`*

**Rule list pending apply**

<img width="927" height="429" alt="07g_icmp_rule_list_pending_apply" src="https://github.com/user-attachments/assets/c393086b-5493-4d35-97da-f1e56da1999f" />


*Figure: `07g_icmp_rule_list_pending_apply.png`*


Ping tests while the rule was being configured (before the change was applied) still succeeded:

**Test 1**

<img width="929" height="430" alt="08a_icmp_test_after_rule_creation" src="https://github.com/user-attachments/assets/716a704d-ac60-4187-b72a-aed986022145" />


*Figure: `08a_icmp_test_after_rule_creation.png`*

**Test 2**


<img width="929" height="437" alt="08c_icmp_retest" src="https://github.com/user-attachments/assets/83b9d224-77ce-4791-ad55-9360f8451b90" />


*Figure: `08c_icmp_retest.png`*

After the change was applied:

```text
ping -c 4 1.1.1.1
4 packets transmitted, 0 received, +4 errors, 100% packet loss
```

**ICMP blocked**

<img width="928" height="440" alt="09_icmp_block_verification" src="https://github.com/user-attachments/assets/a8cf814e-f7e9-4a64-8e6a-6b7807f2a6e9" />


*Figure: `09_icmp_block_verification.png`*


**Status: PASS** – ICMP to `1.1.1.1` was blocked.

### 5.3 DNS and HTTPS after the ICMP rule

**DNS (SERVFAIL observed, see §7)**

<img width="929" height="419" alt="10a_dns_servfail_observed" src="https://github.com/user-attachments/assets/2a4b46f6-4e5a-41e1-b079-7cb1a8efafff" />

*Figure: `10a_dns_servfail_observed.png`*

**DNS (resolved)**

<img width="919" height="417" alt="10b_dns_still_working" src="https://github.com/user-attachments/assets/84d1f29a-918c-43fe-9822-38dc47afb389" />


*Figure: `10b_dns_still_working.png`*

**DNS / HTTPS recheck**

<img width="928" height="432" alt="10c_dns_https_recheck" src="https://github.com/user-attachments/assets/e105dbbb-1e90-468d-86fc-6a0a00b113f5" />


*Figure: `10c_dns_https_recheck.png`*

**HTTPS**

<img width="926" height="431" alt="11_https_still_working" src="https://github.com/user-attachments/assets/cd274c70-1342-4110-9012-5434ab7e382a" />

*Figure: `11_https_still_working.png`*

DNS resolved and HTTPS returned `HTTP/2 200`, showing the ICMP rule did not affect other protocols. **Status: PASS**

### 5.4 HTTP block rule

Rule `LAB2 BLOCK OUTBOUND HTTP`: **Action** Block, **Interface** LAN, **Protocol** TCP, **Source** LAN network, **Destination** any, **Destination port** HTTP (80).

**Rule: organisation**

<img width="455" height="305" alt="12a_http_rule_organisation" src="https://github.com/user-attachments/assets/c2202d2b-8956-48ed-a86e-293f34e49132" />

*Figure: `12a_http_rule_organisation.png`*

**Rule: filter (port 80)**

<img width="450" height="310" alt="12c_http_rule_filter_port80" src="https://github.com/user-attachments/assets/c0374a8a-f47a-4be5-a731-59d4adfa8452" />


*Figure: `12c_http_rule_filter_port80.png`*

**Rules list**

<img width="926" height="387" alt="12b_rules_list_two_rules" src="https://github.com/user-attachments/assets/779744a2-a481-4e2b-b51e-05a094c6a079" />


*Figure: `12b_rules_list_two_rules.png`*

**Rules list (both rules)**

<img width="927" height="407" alt="12d_rules_list_http_rule" src="https://github.com/user-attachments/assets/2227c176-0e29-4e7c-8d05-6b697eadf8c6" />

*Figure: `12d_rules_list_http_rule.png`*

**HTTP test**

<img width="923" height="416" alt="13_http_test_after_rule_creation" src="https://github.com/user-attachments/assets/5b771caf-a835-4f2f-97a8-b3141956f428" />


*Figure: `13_http_test_after_rule_creation.png`*

**HTTPS test**

<img width="925" height="383" alt="14_https_still_working_after_http_block" src="https://github.com/user-attachments/assets/ab05305e-37d8-4f6d-8e0e-2ef75b9e152b" />


*Figure: `14_https_still_working_after_http_block.png`*

HTTPS (TCP/443) continued to work. See §7 regarding the HTTP result.

### 5.5 Firewall log analysis

Reviewed via **Firewall → Log Files → Live View**.

**Live View**

<img width="929" height="419" alt="15_firewall_live_view" src="https://github.com/user-attachments/assets/82486647-8d8f-46c2-baa9-7c0c6593c351" />


*Figure: `15_firewall_live_view.png`*


### 5.6 Restoration

Both lab rules were disabled (greyed out in the rule list) and the changes applied.

**Rules disabled**

<img width="931" height="404" alt="16_connectivity_restored_rules_disabled" src="https://github.com/user-attachments/assets/4c6bef4e-30f0-489b-a1e6-9a7db4e0ed2b" />


*Figure: `16_connectivity_restored_rules_disabled.png`*


## 6. Analysis Questions

**1. Why should blocked traffic be logged?**
Logs prove a rule was triggered and support troubleshooting, monitoring of policy effectiveness, detection of unwanted traffic, and incident investigation.

**2. Why did DNS and HTTPS continue to work after the ICMP rule?**
The rule matched only ICMP to `1.1.1.1`. DNS (UDP/TCP 53) and HTTPS (TCP 443) use different protocols and ports, so they did not match.

**3. Why can HTTPS be allowed while HTTP is blocked?**
HTTP (TCP 80) is unencrypted; HTTPS (TCP 443) is encrypted. Rules are port-specific, so one can be blocked and the other allowed, encouraging secure communication.

**4. What evidence proves the firewall processed the traffic?**
Entries in **Firewall → Log Files → Live View** and the changed client results (ICMP loss after the rule was applied) show the firewall evaluated and acted on traffic.

**5. Why restore connectivity at the end?**
To return the lab to a known operational state so later labs are not affected by leftover restrictions.

## 7. Observations and Limitations

Recorded for accuracy, based on the captured evidence:

- **ICMP timing:** Pings succeeded until the rule was saved with the correct destination and **changes were applied**; blocking was confirmed afterwards (`09_icmp_block_verification.png`).
- **Block response:** The client reported "Destination Host Unreachable" with 100% loss.
- **Transient DNS error:** One `SERVFAIL` was captured on the local resolver (`127.0.0.53`) before resolution succeeded on retry. An ICMP-only rule cannot block DNS, so this is most likely resolver/upstream timing rather than a firewall effect.
- **HTTP test:** The captured `curl` to `http://example.com` returned `HTTP/1.1 200 OK` (`13_http_test_after_rule_creation.png`), so the HTTP block was **not demonstrated** in the saved evidence. A likely cause is that the rule had been saved but not yet applied. The HTTP rule also had **Log** unchecked. Re-test after **Apply changes** with logging enabled to confirm the block.
- **Live View:** The captured view shows allowed (pass) entries only; no blocked-traffic entries are visible. Enable logging on block rules and filter by `action = block` to capture them.
- **Restoration:** Evidence shows both rules disabled; a post-restoration ping screenshot was not captured.

### Suggested re-test

```bash
ping -c 4 1.1.1.1                       # expect 100% loss (ICMP rule enabled)
curl --max-time 10 -I http://example.com   # expect timeout (HTTP rule applied)
curl -I https://example.com                # expect HTTP/2 200
```

## 8. Conclusion

Firewall policies were implemented in OPNsense and validated from a Linux client. The ICMP rule blocked traffic to `1.1.1.1` while DNS and HTTPS stayed functional, demonstrating protocol-specific enforcement. The HTTP/80 rule was created and HTTPS remained available, and rules were disabled to restore the lab. The exercise reinforced the need to apply changes, enable logging on rules of interest, and verify each policy with before/after tests.

## 9. Evidence Index

| Stage | Files |
|---|---|
| Environment | `00a`, `00b`, `01`, `02` |
| Baseline | `03`–`06` |
| ICMP rule | `07a`–`07g`, `08a`–`08c`, `09` |
| DNS / HTTPS checks | `10a`–`10c`, `11` |
| HTTP rule | `12a`–`12d`, `13`, `14` |
| Logging & restore | `15`, `16` |
