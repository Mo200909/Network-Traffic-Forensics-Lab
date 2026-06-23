# DNS Tunneling Forensics Lab

## Overview
Analyzed DNS Iodine tunnel PCAP to identify data exfiltration via DNS protocol abuse.

## Analysis Methodology
Extracted DNS query names using tshark to identify suspicious patterns:
```bash
tshark -r dns-tunnel-iodine.pcap -Y "dns.flags.response == 0" -T fields -e dns.qry.name | sort | uniq -c | sort -nr
```

## Key Findings

### C2 Domain Identified
- **Domain:** pirate.sea
- **Unique Queries:** 100+
- **Protocol:** DNS (UDP port 53)

### DNS Tunneling Indicators
- **Abnormally Long Subdomains:** Queries exceed normal DNS baseline (36+ char payloads vs typical 20–30 chars)
- **Encoded Data:** Base32/Base64 encoded subdomains (e.g., `laegpumiplhhpz12ynd1efljwlkjcgwy.pirate.sea`)
- **Repetitive Patterns:** Iodine tool signature with systematic encoding
- **Query Frequency:** High-volume burst to single C2 domain

### IOCs (Indicators of Compromise)
- **C2 Domain:** pirate.sea
- **Tool:** DNS Iodine (covert exfiltration)
- **Source IP:** 10.0.2.30 (infected host)
- **DNS Resolver:** 10.0.2.20 (port 53)
- **Traffic Pattern:** Query → Response cycles with encoded payloads

## Forensic Significance
DNS tunneling exploits port 53 (typically allowed through firewalls) to establish covert data channels. This PCAP demonstrates real-world C2 communication where attackers exfiltrate data via DNS subdomain labels, bypassing traditional detection.

## Tools Used
- Wireshark (packet inspection)
- tshark (DNS query extraction)
- REMnux VM (isolated analysis environment)
