# Week 2: Network Reconnaissance and Scanning

![Kali Linux](https://img.shields.io/badge/Platform-Kali%20Linux-557C94?logo=kalilinux&logoColor=white)
![Focus](https://img.shields.io/badge/Focus-Reconnaissance%20%7C%20Network%20Scanning-blue)

This repository documents **Week 2** of my Cybersecurity Internship at **NetworkWalks**. The work covers passive and active reconnaissance of `networkwalks.com`, followed by host discovery in an authorized lab network using Nmap and Zenmap.

> **Authorization and safety:** All activities were performed for educational purposes in a controlled environment and against systems for which testing was authorized. Do not run these commands against systems or networks without explicit permission.

## Contents

- [Objectives](#objectives)
- [Tools](#tools)
- [Part 1: Footprinting and reconnaissance](#part-1-footprinting-and-reconnaissance)
- [Part 2: Network scanning with Zenmap](#part-2-network-scanning-with-zenmap)
- [Results](#results)
- [Key learnings](#key-learnings)
- [Conclusion](#conclusion)

## Objectives

- Gather publicly available domain and registration information.
- Fingerprint the technologies used by a website.
- Resolve a domain name and inspect HTTP response headers.
- Identify the presence of a web application firewall (WAF).
- Enumerate DNS records.
- Review local network configuration and subnet information.
- Discover live hosts and visualize their topology in a lab network.

## Tools

| Category | Tools |
| --- | --- |
| Operating systems | Kali Linux, Windows |
| Domain and web reconnaissance | WHOIS, WhatWeb, NSLookup, cURL, WAFW00F |
| DNS enumeration | DNSRecon |
| Network discovery | Nmap, Zenmap |

## Part 1: Footprinting and reconnaissance

The reconnaissance target was `networkwalks.com`. The commands below show the approach used and summarize the observations recorded during the exercise.

### 1. WHOIS

```bash
whois networkwalks.com
```

WHOIS was used to collect publicly available registration details.

| Finding | Value |
| --- | --- |
| Registrar | GoDaddy.com, LLC |
| Creation date | 2019-11-06 |
| Registry expiry date | 2027-11-06 |
| Name servers | `NS6135.HOSTGATOR.COM`, `NS6136.HOSTGATOR.COM` |
| DNSSEC | Unsigned |

![WHOIS output](week2%20screenshots/1.png)

### 2. WhatWeb

```bash
whatweb networkwalks.com
```

WhatWeb was used to identify visible technologies and components. The observed output included Apache, WordPress, WordPress Download Manager, Bootstrap, jQuery, the website IP address, and the site title. The scan reported WordPress version `7.0.4`.

![WhatWeb output](week2%20screenshots/2.png)

### 3. NSLookup

```bash
nslookup networkwalks.com
```

The domain resolved to `192.232.216.135` using the configured DNS resolver.

```text
Server:         8.8.8.8
Address:        8.8.8.8#53

Non-authoritative answer:
Name:           networkwalks.com
Address:        192.232.216.135
```

![NSLookup output](week2%20screenshots/3.png)

### 4. HTTP response headers

```bash
curl -I https://networkwalks.com
```

The header inspection returned HTTP `200` and identified Apache as the web server. It also exposed content type, WordPress-related headers, cookie attributes, policy headers, and a WordPress REST API reference.

![cURL response headers](week2%20screenshots/4.png)

### 5. WAFW00F

```bash
wafw00f networkwalks.com
```

WAFW00F identified **ModSecurity (SpiderLabs)** as the WAF protecting the site.

![WAFW00F output](week2%20screenshots/5.png)

### 6. DNSRecon

```bash
dnsrecon -d networkwalks.com
```

DNSRecon enumerated SOA, NS, MX, A, TXT, SPF, and SRV information.

| Record or detail | Observed value |
| --- | --- |
| A record | `192.232.216.135` |
| Name servers | `ns6135.hostgator.com`, `ns6136.hostgator.com` |
| MX record | `mail.networkwalks.com` |
| DNS server software | BIND `9.16.23-RH` |

The output also reported that no answer was returned for the DNSSEC query.

![DNSRecon output](week2%20screenshots/6.png)

## Part 2: Network scanning with Zenmap

The second part used Zenmap, Nmap's graphical interface, to discover live hosts in an authorized lab network.

### 7. Review the local network configuration

On Windows, the local interface configuration was reviewed with:

```text
ipconfig
```

The captured configuration showed multiple adapters, including:

- Host-only/virtual adapter: `192.168.56.1/24`
- Wi-Fi adapter: `192.168.29.240/24`

These interfaces are separate from the lab subnet scanned below. The scan target was selected in Zenmap as `10.0.0.0/24`.

![Local IP configuration](week2%20screenshots/8.png)

### 8. Configure the Zenmap scan

Zenmap was configured with:

| Setting | Value |
| --- | --- |
| Target | `10.0.0.0/24` |
| Profile | Ping scan |
| Nmap command | `nmap -sn 10.0.0.0/24` |

`-sn` performs host discovery without a port scan.

![Zenmap ping-scan configuration](week2%20screenshots/9.png)

### 9. Discover live hosts

The ping scan identified two live hosts:

| IP address | MAC address |
| --- | --- |
| `10.0.0.1` | `52:54:00:12:35:00` (QEMU virtual NIC) |
| `10.0.0.2` | Not displayed |

```text
Nmap scan report for 10.0.0.1
Host is up.

Nmap scan report for 10.0.0.2
Host is up.

Nmap done: 256 IP addresses (2 hosts up)
```

![Nmap host-discovery output](week2%20screenshots/10.png)

### 10. Visualize the topology

Zenmap's Topology view showed the local host connected to the two discovered hosts:

- `localhost`
- `10.0.0.1`
- `10.0.0.2`

![Zenmap topology](week2%20screenshots/11.png)

## Results

| Activity | Result |
| --- | --- |
| Reconnaissance target | `networkwalks.com` |
| Domain IP | `192.232.216.135` |
| Registrar | GoDaddy.com, LLC |
| Name servers | HostGator (`ns6135`, `ns6136`) |
| Web server | Apache |
| CMS | WordPress |
| Observed WordPress version | `7.0.4` |
| WAF | ModSecurity (SpiderLabs) |
| DNS server software | BIND `9.16.23-RH` |
| Scanned network | `10.0.0.0/24` |
| Live hosts found | 2 |
| Discovered hosts | `10.0.0.1`, `10.0.0.2` |
| MAC address captured | `52:54:00:12:35:00` |

## Key learnings

This exercise provided practical experience with:

- WHOIS information gathering and domain footprinting
- Web technology fingerprinting
- DNS resolution and record enumeration
- HTTP header analysis
- WAF detection
- Nmap ping scanning and live-host discovery
- IP addressing, subnet notation, and virtual network interfaces
- Zenmap-based network topology visualization

## Conclusion

Different reconnaissance tools expose different parts of a target's public footprint. WHOIS and DNS tools revealed registration and infrastructure details, while WhatWeb, cURL, and WAFW00F provided information about the web stack and its protections. Nmap and Zenmap then demonstrated how authorized lab networks can be mapped and their live hosts visualized.

## Internship details

| Field | Value |
| --- | --- |
| Internship | Cybersecurity Internship |
| Organization | NetworkWalks |
| Week | 02 |
| Focus | Network reconnaissance, footprinting, and network scanning |
