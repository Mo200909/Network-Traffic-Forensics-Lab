# DNS Tunneling Forensics Lab

Analyzed a packet capture of Iodine-consistent DNS tunneling traffic with tshark to identify how data was being moved over DNS, and compared it against a benign DNS capture.

## What I Did

Pulled every DNS query name out of the tunnel capture:

```
tshark -r dns-tunnel-iodine.pcap -Y "dns.flags.response == 0" -T fields -e dns.qry.name | sort | uniq -c | sort -nr
```

Pulled every query name out of a normal web-browsing capture as a baseline:

```
tshark -r dns.pcap -T fields -e dns.qry.name | sort | uniq -c | sort -nr
```

## What I Found

- **Capture size:** 438 frames: 222 DNS queries, 212 DNS responses (10 queries unanswered), and 4 ARP frames.
- **Endpoints:** All DNS traffic ran over UDP port 53 between the client (10.0.2.30) and a DNS server (10.0.2.20).
- **Tunnel domain:** pirate.sea, an attacker-controlled domain. Every suspicious query was sent to it.
- **Query names:** All 222 query names were unique (the highest repeat count in the tshark output was 1).
- **Query type:** NULL records, which are rare in normal DNS.
- **Subdomain shape:** Mostly short random-looking labels (e.g. `vaaaakardli.pirate.sea`), with some much longer encoded names containing raw binary-looking data.
- **Example query:** `laegpumiplhhpz12ynd1efljwlkjcgwy.pirate.sea`

## How the Tunnel Works

The client hides data inside the subdomain labels of queries to pirate.sea. The server sends data back inside the NULL record responses, so data moves in both directions.

A DNS server can only reply to queries and cannot start a conversation. The client therefore keeps sending queries so the server has a way to return data.

## Comparison With Normal DNS

| | Normal capture (dns.pcap) | Tunnel capture (dns-tunnel-iodine.pcap) |
|---|---|---|
| Domains | Readable names (www.espn.com, a1.espncdn.com) | One domain: pirate.sea |
| Subdomains | Short, human-readable | Random-looking encoded labels, some very long |
| Record type | Normal lookups | NULL |

## Why This Matters

DNS tunneling abuses the fact that port 53 is almost always allowed through firewalls. Data hidden in subdomain queries looks like normal DNS unless the query names and record types are inspected.

A two-way channel over DNS is consistent with command-and-control (C2) traffic.

## Indicators

- Tunnel domain: pirate.sea
- Tunnel client host: 10.0.2.30
- DNS server: 10.0.2.20
- Record type: NULL
- Tool: Iodine (consistent with the capture filename and the NULL-record pattern; not independently proven)

## Reproducing

- PCAP source: [ADD LINK OR LAB SOURCE]
- The original lab environment was not preserved. Results can be reproduced by running the tshark commands above against the same PCAP files.

## Tools Used

- tshark
- Wireshark
- REMnux VM

## Author

Mofolorunsho Adeleke
GitHub: https://github.com/Mo200909
