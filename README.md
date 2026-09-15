# DNS Tunneling Forensics Lab

Analyzed a packet capture of DNS tunneling traffic to figure out how the data was being smuggled out.

## What I did

Pulled every DNS query name out of the capture with tshark:

```
tshark -r dns-tunnel-iodine.pcap -Y "dns.flags.response == 0" -T fields -e dns.qry.name | sort | uniq -c | sort -nr
```

## What I found

- **C2 domain:** pirate.sea — every suspicious query pointed here
- **100+ unique queries** to that one domain, in a capture of 438 total frames (queries + responses + a couple ARP packets)
- **Weirdly long subdomains** — 36+ characters, way past the normal 20-30 char range
- **Encoded-looking data** in the subdomains (Base32/Base64 style), like `laegpumiplhhpz12ynd1efljwlkjcgwy.pirate.sea`
- **Repetitive patterns** that match how the Iodine tunneling tool encodes its traffic
- All of it went out over port 53 — the port almost every firewall leaves open

## Why this matters

DNS tunneling abuses the fact that port 53 is basically always allowed through firewalls. Attackers hide data inside DNS subdomain queries, which look like normal traffic unless you actually look at the query names closely.

## IOCs

- C2 domain: pirate.sea
- Infected host: 10.0.2.30
- DNS resolver: 10.0.2.20
- Tool signature: DNS Iodine

## Tools used

- Wireshark
- tshark
- REMnux VM

## Author

Mofolorunsho Adeleke
GitHub: https://github.com/Mo200909
