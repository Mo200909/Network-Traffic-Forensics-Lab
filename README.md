# DNS Tunneling Forensics Lab

## Analysis
Analyzed DNS Iodine tunnel PCAP using tshark and Wireshark to identify DNS exfiltration.

## Findings
- **C2 Domain:** pirate.sea
- **Source IP:** 10.0.2.30 (infected host)
- **DNS Resolver:** 10.0.2.20
- **Indicator:** 100+ abnormally long DNS queries with encoded payloads

## IOCs
- Domain: pirate.sea
- Tool: DNS Iodine (covert data exfiltration via port 53)
- Query pattern: Base32/Base64 encoded subdomains

## Tools
- Wireshark
- tshark
- REMnux
