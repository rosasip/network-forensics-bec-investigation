## Network Forensics: Business Email Compromise Investigation

**Tools:** Wireshark · tshark · SMTP Analysis · Network Forensics  
**Skills:** Packet Analysis · Threat Identification · Email Protocol Forensics  
**Context:** CodePath CYB 102 — Unit 1 Project

---

## Overview

This project simulates a real-world **Business Email Compromise (BEC)** investigation. 
Given four packet capture (.pcap) files taken on different days, I analyzed network 
traffic to identify which file contained malicious activity, locate the threat actor's 
IP address, and extract phishing email evidence.

BEC scams have caused over **$26 billion in losses** between 2016–2019 alone, making 
this a highly relevant and practical forensics skill.

---

## Objectives

- Identify malicious SMTP traffic across multiple .pcap files
- Extract phishing email content using Wireshark/tshark
- Determine the attacker's IP address
- Document the investigation methodology

---

## Tools & Environment

- **Wireshark / tshark** — packet capture analysis
- **Ubuntu Linux** — analysis environment
- **SMTP & IMF protocol filters** — traffic isolation

---

## Investigation Methodology

### Step 1 — Initial Triage
Opened all four .pcap files and applied an `smtp` filter to identify which files 
contained email traffic.

| File | IMF Email Packets |
|------|------------------|
| A.pcap | 1 |
| B.pcap | 1 |
| C.pcap | **24** ⚠️ |
| D.pcap | 1 |

C.pcap immediately stood out — 24 emails sent in under 3 seconds is not normal behavior.

### Step 2 — Isolating Email Content
Used the `imf` display filter to extract email metadata from C.pcap:

```bash
tshark -r C.pcap -Y "imf" -T fields -e imf.subject -e imf.from -e imf.to
```

### Step 3 — Identifying the Threat Actor
All 24 emails originated from a single source IP:
- Malicious Actor IP: 10.6.1.104

The actor used the SMTP `HELO` identifier `[173.66.46.112]` and rotated through 
dozens of spoofed sender domains (e.g. `YourLife01@6073.com`, `YourLife97@3953.com`) 
to avoid detection.

### Step 4 — Confirming Malicious Intent
Red flags identified:
- 24 emails sent in ~3 seconds (automated bulk sending)
- Rotating spoofed sender domains across every email
- All recipients were Yahoo addresses (mass targeting)
- Subject lines followed a **sextortion phishing** pattern

---

## Phishing Emails Identified

| Subject Line | Sender | Recipient |
|---|---|---|
| `Pay! - 12345` | YourLife01@6073.com | cadence_khate@yahoo.com |
| `Videos of you! - 122577` | YourLife97@6073.com | crisikat123@yahoo.com |
| `I can destroy everything! - 12345678` | YourLife91@3903.com | mizz_china_01@yahoo.com |

*24 phishing emails total were identified in C.pcap.*

---

## Key Concepts Demonstrated

- **Network Forensics** — analyzing captured traffic to reconstruct events
- **SMTP Protocol** — understanding the email transmission handshake
  (HELO → MAIL FROM → RCPT TO → DATA)
- **BEC / Sextortion Awareness** — recognizing phishing patterns in the wild
- **tshark CLI** — command-line packet analysis and object export
- **IOC Identification** — isolating Indicators of Compromise from network data

---

## Lessons Learned

Analyzing real packet captures reinforced how much information lives in unencrypted 
network traffic. A single IP sending 24 emails in 3 seconds with rotating spoofed 
domains is a clear behavioral indicator — even before reading the content. 
This project deepened my understanding of why network monitoring and anomaly 
detection are critical defensive controls.





