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

![00a_firewall_console_before_restore](screenshots/00a_firewall_console_before_restore.png)

*Figure: `00a_firewall_console_before_restore.png`*

**Firewall LAN/WAN restored**

![00b_firewall_lan_wan_restored](screenshots/00b_firewall_lan_wan_restored.png)

*Figure: `00b_firewall_lan_wan_restored.png`*

**Client IP**

![01_client_ip_verification](screenshots/01_client_ip_verification.png)

*Figure: `01_client_ip_verification.png`*

**Client → Firewall ping**

![02_client_to_firewall_ping](screenshots/02_client_to_firewall_ping.png)

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

![03_baseline_icmp](screenshots/03_baseline_icmp.png)

*Figure: `03_baseline_icmp.png`*

**DNS**

![04_baseline_dns](screenshots/04_baseline_dns.png)

*Figure: `04_baseline_dns.png`*

**HTTP**

![05_baseline_http](screenshots/05_baseline_http.png)

*Figure: `05_baseline_http.png`*

**HTTPS**

![06_baseline_https](screenshots/06_baseline_https.png)

*Figure: `06_baseline_https.png`*

### 5.2 ICMP block rule

Rule `LAB2 BLOCK ICMP TO 1.1.1.1`: **Action** Block, **Interface** LAN, **Direction** In, **Protocol** ICMP, **Source** LAN network, **Destination** `1.1.1.1`, **Log** enabled.

**Login**

![07a_opnsense_login](screenshots/07a_opnsense_login.png)

*Figure: `07a_opnsense_login.png`*

**Dashboard**

![07b_opnsense_dashboard](screenshots/07b_opnsense_dashboard.png)

*Figure: `07b_opnsense_dashboard.png`*

**Default LAN rules**

![07c_firewall_rules_default](screenshots/07c_firewall_rules_default.png)

*Figure: `07c_firewall_rules_default.png`*


**Rule: organisation**

![07d_icmp_rule_organisation](screenshots/07d_icmp_rule_organisation.png)

*Figure: `07d_icmp_rule_organisation.png`*

**Rule: filter**

![07e_icmp_rule_filter](screenshots/07e_icmp_rule_filter.png)

*Figure: `07e_icmp_rule_filter.png`*

**Rule: destination lookup**

![07f_icmp_rule_destination_lookup](screenshots/07f_icmp_rule_destination_lookup.png)

*Figure: `07f_icmp_rule_destination_lookup.png`*

**Rule: destination set**

![08b_icmp_rule_destination_set](screenshots/08b_icmp_rule_destination_set.png)

*Figure: `08b_icmp_rule_destination_set.png`*

**Rule list pending apply**

![07g_icmp_rule_list_pending_apply](screenshots/07g_icmp_rule_list_pending_apply.png)

*Figure: `07g_icmp_rule_list_pending_apply.png`*


Ping tests while the rule was being configured (before the change was applied) still succeeded:

**Test 1**

![08a_icmp_test_after_rule_creation](screenshots/08a_icmp_test_after_rule_creation.png)

*Figure: `08a_icmp_test_after_rule_creation.png`*

**Test 2**

![08c_icmp_retest](screenshots/08c_icmp_retest.png)

*Figure: `08c_icmp_retest.png`*

After the change was applied:

```text
ping -c 4 1.1.1.1
4 packets transmitted, 0 received, +4 errors, 100% packet loss
```

**ICMP blocked**

![09_icmp_block_verification](screenshots/09_icmp_block_verification.png)

*Figure: `09_icmp_block_verification.png`*


**Status: PASS** – ICMP to `1.1.1.1` was blocked.

### 5.3 DNS and HTTPS after the ICMP rule

**DNS (SERVFAIL observed, see §7)**

![10a_dns_servfail_observed](screenshots/10a_dns_servfail_observed.png)

*Figure: `10a_dns_servfail_observed.png`*

**DNS (resolved)**

![10b_dns_still_working](screenshots/10b_dns_still_working.png)

*Figure: `10b_dns_still_working.png`*

**DNS / HTTPS recheck**

![10c_dns_https_recheck](screenshots/10c_dns_https_recheck.png)

*Figure: `10c_dns_https_recheck.png`*

**HTTPS**

![11_https_still_working](screenshots/11_https_still_working.png)

*Figure: `11_https_still_working.png`*

DNS resolved and HTTPS returned `HTTP/2 200`, showing the ICMP rule did not affect other protocols. **Status: PASS**

### 5.4 HTTP block rule

Rule `LAB2 BLOCK OUTBOUND HTTP`: **Action** Block, **Interface** LAN, **Protocol** TCP, **Source** LAN network, **Destination** any, **Destination port** HTTP (80).

**Rule: organisation**

![12a_http_rule_organisation](screenshots/12a_http_rule_organisation.png)

*Figure: `12a_http_rule_organisation.png`*

**Rule: filter (port 80)**

![12c_http_rule_filter_port80](screenshots/12c_http_rule_filter_port80.png)

*Figure: `12c_http_rule_filter_port80.png`*

**Rules list**

![12b_rules_list_two_rules](screenshots/12b_rules_list_two_rules.png)

*Figure: `12b_rules_list_two_rules.png`*

**Rules list (both rules)**

![12d_rules_list_http_rule](screenshots/12d_rules_list_http_rule.png)

*Figure: `12d_rules_list_http_rule.png`*

**HTTP test**

![13_http_test_after_rule_creation](screenshots/13_http_test_after_rule_creation.png)

*Figure: `13_http_test_after_rule_creation.png`*

**HTTPS test**

![14_https_still_working_after_http_block](screenshots/14_https_still_working_after_http_block.png)

*Figure: `14_https_still_working_after_http_block.png`*

HTTPS (TCP/443) continued to work. See §7 regarding the HTTP result.

### 5.5 Firewall log analysis

Reviewed via **Firewall → Log Files → Live View**.

**Live View**

![15_firewall_live_view](screenshots/15_firewall_live_view.png)

*Figure: `15_firewall_live_view.png`*


### 5.6 Restoration

Both lab rules were disabled (greyed out in the rule list) and the changes applied.

**Rules disabled**

![16_connectivity_restored_rules_disabled](screenshots/16_connectivity_restored_rules_disabled.png)

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
