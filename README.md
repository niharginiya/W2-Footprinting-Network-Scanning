# W2-Footprinting-Network-Scanning
# 🛡️ PENETRATION TESTING REPORT

![Kali Linux](https://img.shields.io/badge/Kali-Linux-557C94?logo=kalilinux&logoColor=white) ![Nmap](https://img.shields.io/badge/Nmap-Zenmap-blue) ![Phase](https://img.shields.io/badge/Phases-1%20%26%202-C00000) ![Batch](https://img.shields.io/badge/Batch-B084-595959) ![Status](https://img.shields.io/badge/Week%202-Completed-brightgreen)

### 🔍 Footprinting & Network Scanning Phases

**🎓 W2-PM-FINAL | CYBERSECURITY | NETWORKWALKS**

| Field | Detail |
| --- | --- |
| 👤 **Pentester Name (Cybersecurity Professional)** | **Nihar Giniya** |
| 🏫 **Program/Batch** | B084-Networkwalks |
| 📅 **Date** | 12 November 2026 |
| 📦 **Modules completed** | W2-PM1 (Multiple Kali Tools)<br>W2-PM5 (Zenmap Scanning) |
| 🎯 **Client/Target** | 1. Networkwalks (networkwalks.com) with written permission (Letter of Authorization)<br>2. My own Kali Linux lab machine (localhost, 127.0.0.1) |
| ✅ **Permission secured from client?** | Yes: written Letter of Authorization from Networkwalks |
| 🧭 **Phases covered** | **Phase 1:** Reconnaissance & Footprinting<br>**Phase 2:** Scanning & Network Discovery<br>**Phase 3-5:** Not performed in this report |

# ⚠️ 1. Liability Disclaimer

I have performed these activities only on the systems and devices where I had secured written permission or the devices/systems that I own myself. All these materials are for education and research purpose only. Do not use anything from here to break the law. The instructor, the authors and Networkwalks are not responsible for what you do with this knowledge. Every action you take is your own responsibility. Misuse can lead to criminal charges, heavy fines, loss of your job and a permanent record. In most countries unauthorised access is a crime even when nothing is damaged.

# 📖 2. Introduction

This report covers footprinting the networkwalks.com domain using multiple Kali Linux tools (W2-PM1) and scanning my own lab machine with Nmap and Zenmap (W2-PM5). One module covers the footprinting phase and the other covers the scanning phase, so together they show how an attacker moves from gathering public information to discovering hosts and open services. It is the Week 2 part of my ongoing internship program at Networkwalks.

All commands were run in a Kali Linux virtual machine (VirtualBox). The testing stayed within the scope of the Letter of Authorization: the public website networkwalks.com and its DNS records, and my own machine. Only passive and light information-gathering tools were used. No exploitation, brute-force or denial-of-service activity was performed.

# 🧰 3. Tools Used

| Tool | Purpose |
| --- | --- |
| 🐉 Kali Linux (VirtualBox VM) | Operating system used for all reconnaissance and scanning activities |
| 🔎 whois | Find domain registration details (registrar, dates, name servers) |
| 🧭 nslookup | Query DNS for the IP address and the A, NS, MX and TXT records |
| 🕸️ whatweb | Fingerprint web technologies (server, CMS, plugins, IP) |
| 📨 curl -I | Read the HTTP response headers of the website |
| 🧱 wafw00f | Detect whether a Web Application Firewall protects the site |
| 🗂️ dnsrecon | Enumerate DNS records (SOA, NS, MX, A, TXT, SRV) |
| 📡 Nmap and Zenmap | Host discovery, port and service detection, OS detection and topology view on my own machine |

# 🧪 4. Activities Performed

## 🌐 4.1 Footprinting & Reconnaissance

- 🔎 **whois** (`whois networkwalks.com`): registered through GoDaddy.com, LLC. Created 6 November 2019, updated 12 November 2025, expires 6 November 2027. Name servers are NS6135.HOSTGATOR.COM and NS6136.HOSTGATOR.COM. All four client lock statuses are set and DNSSEC is unsigned.
- 🧭 **nslookup** (A, NS, MX, TXT): the domain resolves to `192.232.216.135`. Mail is handled by mail.networkwalks.com. The SPF record is `v=spf1 +a +mx +ip4:50.87.144.87 +include:websitewelcome.com ~all`, plus a Google site verification token.
- 🕸️ **whatweb** (`whatweb networkwalks.com`): HTTP redirects to HTTPS. Apache, WordPress 7.1.4, WordPress Download Manager 3.3.76, jQuery 3.7.1, Google Tag Manager and a public email address were detected.
- 📨 **curl** (`curl -I https://networkwalks.com`): HTTP/2 200, server Apache (no version). Permissions-Policy and Referrer-Policy are present. HSTS, CSP, X-Frame-Options and X-Content-Type-Options were not seen in the response. The Link header exposes the WordPress REST API endpoint `/wp-json/`.
- 🧱 **wafw00f** (`wafw00f https://networkwalks.com`): the site is behind **ModSecurity (SpiderLabs)**.
- 🗂️ **dnsrecon** (`dnsrecon -d networkwalks.com -t std`): 8 records found, including HostGator name servers, the MX and A records, TXT records and cPanel autodiscover SRV records. Both name servers disclosed BIND version `9.16.23-RH`. No answer for the DNSSEC query. No brute-force option was used.

## 📡 4.2 Network Scanning with Nmap and Zenmap

I scanned my own Kali Linux lab machine (`127.0.0.1`), which is covered by the Letter of Authorization. I started Apache first so the scan had a service to find, and stopped it afterwards. The scan covered one host, so there are no other hosts or MAC addresses to report.

- 📶 **Ping scan** (`nmap -sn 127.0.0.1`): host is up (1 host scanned).
- 🔌 **Service scan** (`sudo nmap -sV 127.0.0.1`): 999 closed ports and one open: `80/tcp open http Apache httpd 2.4.69 (Debian)`.
- 💻 **Aggressive scan** (`sudo nmap -A 127.0.0.1`): default Apache page ("It works"), server header Apache/2.4.69 (Debian), OS guess Linux 5.0 - 6.2 (97%), 0 hops.
- 🗺️ **Zenmap Intense scan** (`nmap -T4 -A -v 127.0.0.1`): 1 open, 0 filtered, 999 closed of 1000 ports; Topology shows a single node, localhost.

# 🚨 5. Risk Analysis / Impact

| # | Risk / Finding | Evidence / Observation | Potential Impact | Risk Level |
| --- | --- | --- | --- | --- |
| 1 | CMS and plugin versions exposed | WhatWeb identified WordPress 7.1.4 and WordPress Download Manager 3.3.76 | Attackers can look up known issues for the exact versions in use | 🟠 **Medium** |
| 2 | Missing browser security headers | curl -I showed no HSTS, CSP, X-Frame-Options or X-Content-Type-Options; /wp-json/ advertised | Weaker browser-side protection against clickjacking, content sniffing and downgrade attacks | **● Medium** |
| 3 | DNS and mail configuration details exposed | dnsrecon found BIND 9.16.23-RH, cPanel SRV records and an SPF record ending in ~all | Helps profile the infrastructure; soft-fail SPF makes email spoofing easier | **● Medium** |
| 4 | Server IP and hosting identifiable | nslookup resolved the site to 192.232.216.135 on HostGator shared hosting | Shows where the service is hosted | 🟢 **Low** |
| 5 | WAF technology identifiable | wafw00f identified ModSecurity (SpiderLabs) | Reveals part of the security architecture (the WAF is a positive control) | **● Low** |
| 6 | DNSSEC not enabled | whois: DNSSEC unsigned; dnsrecon: no DNSSEC answer | DNS answers are not cryptographically validated | **● Low** |
| 7 | Local web service with version banner | Nmap found 80/tcp open, Apache httpd 2.4.69 (Debian), default page | Unneeded service and version banner increase attack surface | **● Low** |

> ℹ️ **Risk level key:** 🔴 Critical | 🟠 Medium | 🟢 Low

The risks above are observations from the footprinting and scanning exercises, not confirmed vulnerabilities. No exploitation or vulnerability validation was performed. Further authorized testing would be required to confirm any actual vulnerability.

# 🔧 6. Recommendations

1. 🕵️ **Reduce technology disclosure:** remove the WordPress generator meta tag and review what CMS, plugin and version information is public.
2. 🔄 **Keep software updated:** update WordPress core, plugins and libraries and review them against security advisories.
3. 🧱 **Add missing security headers:** HSTS, CSP, X-Frame-Options and X-Content-Type-Options, and the SameSite cookie attribute.
4. ✉️ **Harden email authentication:** move SPF from ~all to -all once senders are confirmed, and publish DKIM and DMARC records.
5. 🌐 **Harden DNS:** hide the BIND version string, enable DNSSEC if supported, and review DNS records regularly.
6. 🔥 **Properly configure and monitor the WAF:** keep ModSecurity enabled, updated and monitored.
7. 🚫 **Stop unneeded services and hide banners:** set ServerTokens Prod and ServerSignature Off, remove default pages, and allow only required ports.
8. 🔭 **Perform regular internal network discovery:** investigate unknown devices and keep network documentation up to date.
9. ✅ **Perform security testing only with authorization.**

# 🏁 7. Conclusion

During Week 2 of my Cybersecurity & Ethical Hacking internship, I completed practical activities covering footprinting, reconnaissance and network scanning. Six Kali Linux tools showed that WordPress and plugin versions, DNS software details and missing security headers are visible to anyone, while HTTPS redirection, a WAF, HttpOnly cookies and domain locks are in place. Nmap and Zenmap showed how to discover a host, its open port and service version, an OS guess and its topology. My scan covered one machine only, so a future scan of a full subnet would give a more complete picture. The exercises taught me that findings must be documented clearly with evidence and that testing must always stay within an authorized scope.

# 📸 8. Evidences Collected

*Screenshots collected as evidence during the activities (stored in the `screenshots/` folder):*

whois networkwalks.com (Screenshots/01_whois.png)

[![nslookup networkwalks.com](screenshots/2_nslookup_a.png)](screenshots/2_nslookup_a.png)

[![nslookup -type=NS](screenshots/3_nslookup_ns.png)](screenshots/3_nslookup_ns.png)

[![nslookup -type=MX](screenshots/4_nslookup_mx.png)](screenshots/4_nslookup_mx.png)

[![nslookup -type=TXT](screenshots/5_nslookup_txt.png)](screenshots/5_nslookup_txt.png)

[![whatweb networkwalks.com](screenshots/6_whatweb.png)](screenshots/6_whatweb.png)

[![curl -I https://networkwalks.com](screenshots/7_curl.png)](screenshots/7_curl.png)

[![wafw00f https://networkwalks.com](screenshots/8_wafw00f.png)](screenshots/8_wafw00f.png)

[![dnsrecon -d networkwalks.com -t std](screenshots/9_dnsrecon.png)](screenshots/9_dnsrecon.png)

[![nmap -sn 127.0.0.1](screenshots/10_nmap_ping_scan.png)](screenshots/10_nmap_ping_scan.png)

[![Zenmap Ping scan](screenshots/11_zenmap_ping_scan.png)](screenshots/11_zenmap_ping_scan.png)

[![sudo nmap -sV 127.0.0.1](screenshots/12_nmap_sV.png)](screenshots/12_nmap_sV.png)

[![sudo nmap -A 127.0.0.1](screenshots/13_nmap_A.png)](screenshots/13_nmap_A.png)

[![Zenmap Intense scan - Nmap Output](screenshots/14_zenmap_intense_output.png)](screenshots/14_zenmap_intense_output.png)

[![Zenmap - Ports / Hosts](screenshots/15_zenmap_ports_hosts.png)](screenshots/15_zenmap_ports_hosts.png)

[![Zenmap - Host Details](screenshots/16_zenmap_host_details.png)](screenshots/16_zenmap_host_details.png)

[![Zenmap - Topology](screenshots/17_zenmap_topology.png)](screenshots/17_zenmap_topology.png)

<p align="center">✨ <b>End of Report</b> ✨</p>
