# DNS Tunneling Forensics Lab

Analyzed a packet capture of Iodine DNS tunneling traffic with tshark to identify how data was being moved over DNS.

## What I did

Pulled every DNS query name out of the capture with tshark:

    tshark -r dns-tunnel-iodine.pcap -Y "dns.flags.response == 0" -T fields -e dns.qry.name | sort | uniq -c | sort -nr

## What I found

- **Tunnel domain:** pirate.sea. Every suspicious query targeted it.
- **Capture size:** 438 frames: 222 DNS queries, 212 DNS responses, 4 ARP.
- **Query names:** all 222 query names were unique (highest repeat count in the tshark output was 1), so the traffic doesn't look like normal repeated lookups.
- **Query type:** NULL records, which are rare in normal DNS.
- **Subdomain shape:** mostly short labels (e.g. `vaaaakardli.pirate.sea`), with some much longer encoded names, including raw binary-looking data.
- **Example query:** `laegpumiplhhpz12ynd1efljwlkjcgwy.pirate.sea`
- Traffic ran over UDP port 53 between the client and resolver.

## Why this matters

DNS tunneling abuses the fact that port 53 is almost always allowed through firewalls. Data hidden in subdomain queries looks like normal DNS unless you inspect the query names.

## Indicators

- Tunnel domain: pirate.sea
- Tunnel client host: 10.0.2.30
- DNS resolver: 10.0.2.20
- Tool: Iodine (from the capture filename and the NULL-record pattern)

## Tools used

- Wireshark
- tshark
- REMnux VM

## Author

Mofolorunsho Adeleke
GitHub: https://github.com/Mo200909
