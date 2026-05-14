# 08 — Sources and Further Reading

All references from Section 2, organized by topic, with additional resources for deeper learning.

---

## Networks and Internet History

### Essential Reading
- [History of the Internet — Wikipedia](https://en.wikipedia.org/wiki/History_of_the_Internet) — Comprehensive, well-cited article with timeline
- [A Brief History of the Internet — Internet Society](https://www.internetsociety.org/internet/history-internet/brief-history-internet/) — Written by Vint Cerf, Bob Kahn, and others who built it
- [How the Internet Works — Khan Academy](https://www.khanacademy.org/computing/computers-and-the-internet) — Free interactive course with videos

### Network Types and Topology
- [Types of Computer Networks — GeeksforGeeks](https://www.geeksforgeeks.org/types-of-computer-networks/)
- [Network Topology — Cisco](https://www.cisco.com/c/en/us/solutions/small-business/resource-center/networking/network-topologies.html)

### Video
- [How the Internet Works — Crash Course (YouTube)](https://www.youtube.com/watch?v=AEaKrq3SpW8)
- [The Internet: Packets, Routing & Reliability — Code.org (YouTube)](https://www.youtube.com/watch?v=AYdF7b3nMto)

---

## TCP/IP and Packets

### Articles
- [TCP/IP Model — GeeksforGeeks](https://www.geeksforgeeks.org/tcp-ip-model/)
- [How Does the Internet Work? (Stanford Paper)](https://web.stanford.edu/class/msande91si/www-spr04/readings/week1/InternetWhitepaper.htm) — Classic, accessible whitepaper
- [TCP vs UDP — Cloudflare](https://www.cloudflare.com/learning/ddos/glossary/user-datagram-protocol-udp/)
- [IP Addressing and Subnetting — Cisco](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html)
- [IPv6 Overview — ARIN](https://www.arin.net/resources/guide/ipv6/)
- [NAT Explained — Cloudflare](https://www.cloudflare.com/learning/network-layer/what-is-nat/)

### Deep Dives
- [Beej's Guide to Network Programming](https://beej.us/guide/bgnet/) — Free; excellent deep dive into TCP/IP sockets for programmers
- [TCP — RFC 793](https://tools.ietf.org/html/rfc793) — The original specification; dense but authoritative

### Video
- [Packet Switching — Computerphile (YouTube)](https://www.youtube.com/watch?v=AYdF7b3nMto)
- [TCP/IP and Subnet Masking — Professor Messer (YouTube)](https://www.youtube.com/watch?v=EkNq4TrHP_U)

---

## Application Layer Protocols

### HTTP
- [HTTP — MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/HTTP) — The definitive reference for web developers
- [HTTP Status Codes — httpstatuses.com](https://httpstatuses.com/) — Every status code with explanations
- [HTTP Methods — MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
- [HTTP Headers — MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)
- [HTTP/2 Explained — Cloudflare](https://www.cloudflare.com/learning/performance/http2-vs-http1.1/)
- [HTTP/3 — Cloudflare](https://www.cloudflare.com/learning/performance/what-is-http3/)

### DNS
- [DNS Explained — Cloudflare](https://www.cloudflare.com/learning/dns/what-is-dns/) — Excellent illustrated guide
- [How DNS Works — Comic (howdns.works)](https://howdns.works/) — A visual comic explaining DNS resolution
- [DNS Record Types — Cloudflare](https://www.cloudflare.com/learning/dns/dns-records/)
- [DNS — RFC 1034 and 1035](https://tools.ietf.org/html/rfc1034) — Original specification

### Other Protocols
- [FTP — GeeksforGeeks](https://www.geeksforgeeks.org/file-transfer-protocol-ftp-in-application-layer/)
- [DHCP — Cloudflare](https://www.cloudflare.com/learning/network-layer/what-is-dhcp/)
- [SMTP and Email Protocols — MDN](https://developer.mozilla.org/en-US/docs/Learn/Server-side/First_steps/Introduction)
- [SSH — Explained (SSH.com)](https://www.ssh.com/academy/ssh) — The company that invented SSH explains how it works

---

## Peer-to-Peer Networks

### BitTorrent
- [BitTorrent Protocol Specification](https://www.bittorrent.org/beps/bep_0003.html) — The original spec by Bram Cohen
- [How BitTorrent Works — HowStuffWorks](https://computer.howstuffworks.com/bittorrent.htm)
- [BitTorrent — Wikipedia](https://en.wikipedia.org/wiki/BitTorrent)

### DHT
- [Kademlia DHT — Wikipedia](https://en.wikipedia.org/wiki/Kademlia)
- [Distributed Hash Tables — MIT OpenCourseWare](https://ocw.mit.edu/courses/6-824-distributed-computer-systems-engineering-spring-2006/)

### Blockchain
- [Bitcoin Whitepaper — Satoshi Nakamoto](https://bitcoin.org/bitcoin.pdf) — The original; surprisingly readable
- [How Blockchain Works — 3Blue1Brown (YouTube)](https://www.youtube.com/watch?v=bBC-nXj3Ng4) — Best visual explanation

---

## curl, telnet, netcat

### curl
- [curl Documentation — curl.se](https://curl.se/docs/manpage.html) — The official manual
- [curl Cookbook — curl.se](https://catonmat.net/cookbooks/curl) — Common recipes
- [httpbin.org](https://httpbin.org/) — Free HTTP testing service used in exercises
- [Everything curl — free book](https://everything.curl.dev/) — Comprehensive guide

### netcat
- [netcat — The TCP/IP Swiss Army Knife](https://nc110.sourceforge.io/) — Official page
- [netcat Examples — GeeksforGeeks](https://www.geeksforgeeks.org/introduction-to-netcat/)
- [Ncat — nmap's version](https://nmap.org/ncat/) — Windows-compatible with more features

### General Tools
- [explainshell.com](https://explainshell.com/) — Paste any command and get a plain-English explanation
- [badssl.com](https://badssl.com/) — Test how your browser handles various TLS failure modes

---

## Security and Encryption

### Conceptual
- [How HTTPS Works — comic (howhttps.works)](https://howhttps.works/) — Illustrated guide; highly recommended
- [TLS 1.3 Overview — Cloudflare](https://www.cloudflare.com/learning/ssl/why-use-tls-1.3/)
- [Public Key Cryptography — Computerphile (YouTube)](https://www.youtube.com/watch?v=GSIDS_lvRv4)
- [TLS Handshake — Cloudflare](https://www.cloudflare.com/learning/ssl/what-happens-in-a-tls-handshake/)

### Certificates
- [SSL/TLS Certificates — DigiCert](https://www.digicert.com/what-is-an-ssl-certificate)
- [Let's Encrypt — How It Works](https://letsencrypt.org/how-it-works/) — The free CA used on most of the Internet
- [Certificate Transparency — Google](https://certificate.transparency.dev/)

### openssl
- [openssl Documentation](https://www.openssl.org/docs/manmaster/man1/openssl.html) — Official reference
- [openssl Cookbook — Ivan Ristić (free book)](https://www.feistyduck.com/library/openssl-cookbook/) — Excellent practical guide
- [testssl.sh](https://testssl.sh/) — Script to test TLS configuration of a server

### Testing Sites
- [badssl.com](https://badssl.com/) — Test pages with intentionally broken TLS configurations
- [SSL Labs Server Test — Qualys](https://www.ssllabs.com/ssltest/) — Grade a website's TLS implementation

---

## Books

| Book | Author | Notes |
|------|--------|-------|
| *Computer Networks* | Tanenbaum & Wetherall | Standard university networking textbook; comprehensive |
| *Computer Networking: A Top-Down Approach* | Kurose & Ross | Widely used; starts with application layer and works down |
| *TCP/IP Illustrated, Vol. 1* | W. Richard Stevens | Classic deep dive into TCP/IP protocols with packet traces |
| *Beej's Guide to Network Programming* | Brian "Beej" Hall | Free online; practical socket programming in C |
| *The Practice of Network Security Monitoring* | Richard Bejtlich | Real-world network security analysis |
| *Crypto* | Steven Levy | History of the cypherpunk movement and modern encryption; narrative nonfiction |

---

## Interactive Learning

- [Cisco Networking Academy (free courses)](https://www.netacad.com/) — Free networking courses from Cisco
- [Professor Messer's CompTIA Network+ (YouTube)](https://www.youtube.com/user/professormesser) — Free study material for networking certification
- [Wireshark (free tool)](https://www.wireshark.org/) — Capture and inspect actual network packets on your machine; best way to see TCP/IP in action
- [Networking Fundamentals — Microsoft Learn (free)](https://learn.microsoft.com/en-us/training/paths/network-fundamentals/)

---

*Back to Section Overview: [07 — Quiz and Checkpoints](07-quiz-and-checkpoints.md)*
*Next Section: [Section 3 — How the Web Works](../section-03-how-the-web-works/01-dns-and-domain-names.md)*
