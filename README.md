# Network Traffic Analysis with Wireshark
## PCAP Forensics & Protocol Investigation

---

| Field | Details |
|-------|---------|
| **Name** | Aditya Upmanyu |
| **Course** | Ethical Hacking — JOVAC |
| **Assignment** | Assignment 2 — Network Traffic Analysis with Wireshark |
| **Date** | July 25, 2026 |
| **File Analyzed** | traffic.pcapng |



## Objective

To perform a comprehensive forensic analysis of a real-world network packet capture (PCAPNG) file using Wireshark / TShark. The objective is to extract actionable intelligence including protocol distribution, IP communication patterns, MAC-to-IP mappings, vendor identification, and anomaly indicators — demonstrating proficiency in network traffic investigation as part of an ethical hacking skillset.

---

## Tools Used

| Tool | Version | Purpose |
|------|---------|---------|
| Wireshark / TShark | 4.2.2 | Packet dissection and filtering |
| `capinfos` | 4.2.2 | Capture metadata extraction |
| Python 3 | 3.12 | Report generation & automation |
| ReportLab | Latest | Professional PDF generation |

---

## Capture Overview

| Metric | Value |
|--------|-------|
| File Format | pcapng (Wireshark v1.0) |
| Total Packets | 26,456 |
| Capture Duration | 348.11 seconds (~5 min 48 sec) |
| First Packet | 2021-07-26 12:53:50 UTC |
| Last Packet | 2021-07-26 12:59:38 UTC |
| Capture OS | Windows 10 (build 19042) |
| Capture Tool | Dumpcap (Wireshark 3.4.7) |
| Average Packet Rate | 75 packets/sec |

---

## Questions and Answers

### E1 — Number of DHCP Messages
- **Wireshark Filter:** `dhcp or bootp`
- **Answer:** **5 packets**
- **Breakdown:** 1 Inform, 1 Discover, 1 Offer, 1 Request, 1 ACK

---

### E2 — Number of ARP Messages
- **Wireshark Filter:** `arp`
- **Answer:** **680 packets**

---

### E3 — IP Address That Accessed baidu.com
- **Wireshark Filter:** `dns.qry.name contains "baidu"`
- **Answer:** **10.103.51.159**
- **Evidence:** IP 10.103.51.159 issued DNS queries to DNS server 10.103.0.20 for `www.baidu.com` (frame 20754) and numerous baidu subdomains, followed by HTTP/HTTPS connections to baidu servers.

---

### E4 — Number of Packets Sent by Source IP 10.103.0.20
- **Wireshark Filter:** `ip.src == 10.103.0.20`
- **Answer:** **469 packets**

---

### E5 — Number of UDP Packets
- **Wireshark Filter:** `udp`
- **Answer:** **9,102 packets**

---

### E6 — Number of SMB Packets
- **Wireshark Filter:** `smb or smb2`
- **Answer:** **518 packets**

---

### E7 — Number of Packets Sent to Destination IP 10.103.0.20
- **Wireshark Filter:** `ip.dst == 10.103.0.20`
- **Answer:** **547 packets**

---

### E8 — IPv4 Traffic Packet Count
- **Wireshark Filter:** `ip`
- **Answer:** **24,906 packets**

---

### E9 — Last SMB Packet and Its Source IP
- **Wireshark Filter:** `smb or smb2` → last frame
- **Frame Number:** 26452
- **Source IP:** **10.103.50.202**
- **Protocol Stack:** eth → ip → udp → nbdgm → smb (SMB_NETLOGON broadcast)
- **Destination:** 10.103.255.255 (subnet broadcast)

---

### E10 — MAC Address of IP 151.139.128.14
- **Wireshark Filter:** `ip.src == 151.139.128.14`
- **Answer:** **00:1c:7f:6c:96:3f**
- **Vendor (OUI):** Check Point Software Technologies Ltd.
- **Note:** This is the gateway/firewall MAC seen in the capture routing traffic for external IP 151.139.128.14.

---

### E11 — Protocol in Packet Number 416
- **Wireshark Filter:** `frame.number == 416`
- **Protocol Stack:** eth → ethertype → ip → udp → **DNS**
- **Answer:** **DNS**



### E12 — Destination IP Address in Packet Number 416
- **Wireshark Filter:** `frame.number == 416`
- **Answer:** **10.103.0.20**
- **Context:** This is a DNS query from 10.103.51.159 to the local DNS server 10.103.0.20 on UDP port 53.

---

### E13 — Source IP Address of the First HTTP Packet
- **Wireshark Filter:** `http` → frame 19007
- **Answer:** **10.103.51.159**
- **Details:** GET request to `ocsp.comodoca.com` on frame 19007.

---

### E14 — Source Port of the First HTTP Packet
- **Wireshark Filter:** `http` → frame 19007 → tcp.srcport
- **Answer:** **64079**

---

### E15 — Capture Duration in Seconds
- **Method:** `capinfos traffic.pcapng`
- **Answer:** **348.109916 seconds** (≈ 5 minutes 48 seconds)

---

### E16 — Number of NBNS Packets
- **Wireshark Filter:** `nbns`
- **Answer:** **963 packets**

---

### E17 — Number of TCP Packets with Source Port 443
- **Wireshark Filter:** `tcp.srcport == 443`
- **Answer:** **7,275 packets**

---

### E18 — Number of TCP Packets with Destination Port 443
- **Wireshark Filter:** `tcp.dstport == 443`
- **Answer:** **5,966 packets**

---

### E19 — Number of Packets Sent to Destination IP 204.79.197.200
- **Wireshark Filter:** `ip.dst == 204.79.197.200`
- **Answer:** **116 packets**
- **Note:** 204.79.197.200 is a Microsoft/Bing-related IP (part of the MSN/Bing CDN).

---

### E20 — MAC Address of IP 204.79.197.200 and Vendor Identification
- **Wireshark Filter:** `ip.src == 204.79.197.200` → eth.src
- **MAC Address:** **00:1c:7f:6c:96:3f**
- **OUI (First 3 Octets):** 00:1C:7F
- **Vendor:** **Check Point Software Technologies Ltd.**
- **Note:** The MAC belongs to the local Check Point gateway/firewall — all external communications to 204.79.197.200 pass through this device.

---

## Summary Table

| # | Question | Wireshark Filter | Answer |
|---|----------|-----------------|--------|
| E1 | DHCP message count | `dhcp or bootp` | 5 |
| E2 | ARP message count | `arp` | 680 |
| E3 | IP that accessed baidu.com | `dns.qry.name contains "baidu"` | 10.103.51.159 |
| E4 | Packets from source 10.103.0.20 | `ip.src == 10.103.0.20` | 469 |
| E5 | UDP packet count | `udp` | 9,102 |
| E6 | SMB packet count | `smb or smb2` | 518 |
| E7 | Packets to dest 10.103.0.20 | `ip.dst == 10.103.0.20` | 547 |
| E8 | IPv4 traffic count | `ip` | 24,906 |
| E9 | Last SMB packet source IP | `smb or smb2` (last) | Frame 26452 → 10.103.50.202 |
| E10 | MAC of IP 151.139.128.14 | `ip.src == 151.139.128.14` | 00:1c:7f:6c:96:3f |
| E11 | Protocol in packet 416 | `frame.number == 416` | DNS |
| E12 | Dest IP in packet 416 | `frame.number == 416` | 10.103.0.20 |
| E13 | Source IP of first HTTP packet | `http` (first frame) | 10.103.51.159 |
| E14 | Source port of first HTTP packet | `http` (first frame) | 64079 |
| E15 | Capture duration (seconds) | capinfos | 348.109916 s |
| E16 | NBNS packet count | `nbns` | 963 |
| E17 | TCP packets, src port 443 | `tcp.srcport == 443` | 7,275 |
| E18 | TCP packets, dst port 443 | `tcp.dstport == 443` | 5,966 |
| E19 | Packets to dest 204.79.197.200 | `ip.dst == 204.79.197.200` | 116 |
| E20 | MAC + vendor of 204.79.197.200 | `ip.src == 204.79.197.200` | 00:1c:7f:6c:96:3f — Check Point Software Technologies |

---

## Key Observations & Security Insights

1. **Network Infrastructure:** A Check Point Software Technologies firewall/gateway (MAC: `00:1c:7f:6c:96:3f`) acts as the default gateway for all external traffic in this network.

2. **DNS Server:** 10.103.0.20 serves as the local DNS resolver. All client DNS queries (including baidu.com, Bing/MSN CDN) resolve through this server.

3. **Baidu Activity:** Host 10.103.51.159 performed extensive browsing of baidu.com and its subdomains (news, maps, translate, drive, etc.) — indicating a user actively browsing the Chinese search engine.

4. **HTTPS Traffic Dominance:** TCP port 443 traffic (5,966 outbound + 7,275 inbound) far exceeds HTTP, showing modern encrypted web usage.

5. **SMB Broadcast Traffic:** The last SMB packet is a NETLOGON broadcast — typical Windows domain infrastructure traffic.

6. **NBNS Prevalence:** 963 NBNS packets indicate an active Windows NetBIOS environment with multiple hosts performing name resolution.

---

## Conclusion

This analysis successfully extracted 20 distinct data points from the `traffic.pcapng` capture using TShark display filters. The network segment under analysis is a Windows-based enterprise LAN protected by a Check Point firewall. Key activities include DNS resolution, heavy HTTPS browsing (Bing/MSN, Baidu), SMB domain activity, and NBNS Windows name resolution. All answers were derived directly from the packet capture with zero assumptions or guesswork.

---

*Report generated by: Aditya Upmanyu | JOVAC Ethical Hacking Course | Assignment 2 | July 25, 2026*
