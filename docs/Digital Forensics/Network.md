# Network

## Background
<blah>

### Network Analysis Tools
<foo bar>

#### tshark
<foo bar>

#### Wireshark
Wireshark is a network traffic analyser.
Below are some of my favorite wireshark filters.
I reccommend bookmarking these and creating a custome profile for them. 

## Cheatsheet

| **Filter** | **Description** |
| --------------|-------------------|
| `http.request.full_uri == "<url>"` | Search packets for specific url | 
| `pktmon filter add <blah>` | Create a pktmon filter |
| `(http.request or tls.handshake.type eq 1  and !(ssdp))` | basic web request filter which removes simple service discovery protocol traffic |
| `(http.request or tls.handshake.type eq 1 or tcp.flags eq 0x0002 or dns) and !(ssdp)` | A web traffic filer which includes dns and tcp traffic |
| `urlencoded-form and http.request.method == POST and urlencoded-form.key == "password"` | Filter for http post requests to a form with the key word of password |
| `ip.addr == 192.168.1.5 and udp.port not in {53, 123} and tcp.port != 443` | Filter for all traffic involving 192.168.1.5 but not DNS, NTP or HTTPS traffic |
| `not arp and !(udp.port == 53)` | No arp or DNS traffic |