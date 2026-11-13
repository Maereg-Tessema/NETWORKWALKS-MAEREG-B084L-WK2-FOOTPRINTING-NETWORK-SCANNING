# Week 2 Footprinting & Network Scanning

Networkwalks Cybersecurity Internship | Batch B084L

## Overview

This project covers Phase 1 (Footprinting & Reconnaissance) and Phase 2 (Scanning) of a penetration test. All activities were performed under written authorization (Letter of Authorization, Ref. NW-LOA-B082-017) against **networkwalks.com**, and all network scanning was performed only on my own home LAN.

## Modules Completed

- W2-PM1: Footprinting with multiple Kali tools
- W2-PM5: Network Scanning with Zenmap

## Tools Used

| Tool | Purpose |
|---|---|
| whois | Domain registration lookup |
| whatweb | Website technology fingerprinting |
| nslookup | DNS resolution |
| curl -I | HTTP header analysis |
| wafw00f | Web Application Firewall (WAF) detection |
| dnsrecon | Full DNS record enumeration |
| Zenmap / Nmap | Host discovery & network topology mapping |

## Activities Performed

### 1. whois

Looked up the domain registration record for networkwalks.com.

**Findings:**
- Registrar: GoDaddy.com, LLC
- Created: 6 November 2019
- Expires: 6 November 2027
- Name Servers: NS6135.HOSTGATOR.COM, NS6136.HOSTGATOR.COM (hosted on HostGator, registered through GoDaddy)
- Registrant identity hidden behind a privacy proxy (Domains By Proxy, LLC)

<img width="1918" height="1048" alt="image" src="https://github.com/user-attachments/assets/60ad980e-25b4-4548-93ca-7358af59c50a" />

### 2. whatweb

Fingerprinted the website's underlying technology.

**Findings:**
- Web server: Apache
- CMS: WordPress 7.1.4
- Plugin: WordPress Download Manager 3.3.76
- Site IP: 192.232.216.135
- Exposed email: info@networkwalks.com

<img width="1917" height="994" alt="image" src="https://github.com/user-attachments/assets/d3a6efe0-b589-4ca3-a278-f415a0726485" />

### 3. nslookup

Resolved the domain name to its IP address.

**Findings:**
- networkwalks.com → 192.232.216.135 (confirms the IP found via whatweb)

<img width="1914" height="1032" alt="image" src="https://github.com/user-attachments/assets/c5dbfbe3-38c1-4c42-988c-9504aada89e2" />

### 4. curl -I

Retrieved the HTTP response headers from the live site.

**Findings:**
- Confirms Server: Apache
- Exposed WordPress REST API endpoint: /wp-json/
- Cookie `__wpdm_client` confirms the WP Download Manager plugin
- `x-nginx-cache: WordPress` indicates an Nginx caching layer in front of Apache
- Permissions-Policy header shows active restrictions on sensitive browser features (a defensive measure)

<img width="960" height="925" alt="image" src="https://github.com/user-attachments/assets/afdc94e7-0848-485f-9026-ca6b059bc22e" />

### 5. wafw00f

Checked whether the site is protected by a Web Application Firewall.

**Findings:**
- The site is behind ModSecurity (SpiderLabs) WAF — a defensive strength
<img width="1914" height="1030" alt="image" src="https://github.com/user-attachments/assets/63dda886-6d69-4224-b7db-50518cfe7337" />


### 6. dnsrecon

Performed full DNS enumeration against the domain.

**Findings:**
- SOA / NS records confirm ns6135.hostgator.com and ns6136.hostgator.com
- BIND version 9.16.23-RH exposed on both name servers
- MX record: mail.networkwalks.com (192.232.216.135)
- SPF record present (`v=spf1 +a +mx +ip4:50.87.144.87 +include:websitewelcome.com ~all`), helping prevent email spoofing
- 8 SRV records found, pointing to cpanelemaildiscovery.cpanel.net — confirms cPanel-based hosting infrastructure

<img width="1918" height="1039" alt="image" src="https://github.com/user-attachments/assets/f32b0509-a501-40a2-9b09-90ee827f4db9" />


### 7. Zenmap — Network Scanning (own home LAN)

Performed host discovery on my own home Wi-Fi network (192.168.8.0/24).

**My IP & subnet:** 192.168.8.100 / 255.255.255.0 (192.168.8.0/24)

**Live hosts found:** 3

| IP Address | MAC Address | Device |
|---|---|---|
| 192.168.8.1 | A2:B6:44:3B:83:C0 | Router (Huawei Mobile WiFi) |
| 192.168.8.100 | B0-3C-DC-A6-82-B3 | My PC |
| 192.168.8.102 | 62:AA:94:F1:A5:17 | Phone (ZTE Blade A33) |

<img width="1917" height="1074" alt="image" src="https://github.com/user-attachments/assets/96d90430-1627-41ab-b4ad-c561dc72218d" />

<img width="1915" height="1078" alt="image" src="https://github.com/user-attachments/assets/95c9eb2a-c782-4ec5-b99c-ea1a94926103" />

**Network Topology:**

<img width="1912" height="1072" alt="image" src="https://github.com/user-attachments/assets/d5a1743e-c849-4eae-9da3-a0414efc313d" />


## Risk Analysis

| Finding | Risk Level | Notes |
|---|---|---|
| WordPress & plugin version exposed | Low-Medium | Specific versions could be checked against known CVEs |
| BIND version exposed on name servers | Low | Version disclosure could assist targeted attacks |
| Email address exposed in page metadata | Low | Minor information disclosure, common on business sites |
| /wp-json/ REST API endpoint exposed | Low | Standard WordPress behavior, not inherently a vulnerability |
| ModSecurity WAF present | Positive | Active defense against common web attacks |
| SPF record configured | Positive | Helps prevent email spoofing |
| Domain registrant privacy protected | Positive | Reduces exposure of personal contact info |

## Recommendations

- Keep WordPress core and all plugins updated to the latest stable versions.
- Periodically review exposed HTTP headers and metadata for unnecessary information disclosure.
- Continue using a WAF and privacy-protected domain registration, both are good existing practices.
- On a home/LAN level, keep router firmware updated and use strong Wi-Fi credentials, since any device joining the network can perform the same kind of scan.

## Conclusion

This exercise demonstrated the first two phases of a penetration test: footprinting (passive, information-gathering reconnaissance) and scanning (active host discovery). The target, networkwalks.com, showed generally good security hygiene (WAF present, SPF configured, registrant privacy protected), with only minor, low-risk information disclosures typical of most public WordPress sites. On my home network, the scan correctly identified all 3 connected devices, confirming an understanding of basic network discovery.

## Disclaimer

All testing was performed only on authorized targets. Reconnaissance activities against networkwalks.com were conducted under written permission (Letter of Authorization, Networkwalks Cybersecurity Internship). Network scanning was performed only against my own home LAN, which I own and control. No exploitation, unauthorized access, or disruptive activity was attempted.
