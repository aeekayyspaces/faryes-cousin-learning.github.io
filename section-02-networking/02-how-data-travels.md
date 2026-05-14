# 02 — How Data Travels: Packets, TCP/IP, and the Network Stack

## Learning Objectives

By the end of this page, you will be able to:

- Explain what a packet is and why data is broken into packets
- Describe the four layers of the TCP/IP model and what each does
- Distinguish between TCP and UDP and when each is appropriate
- Explain what an IP address is and the difference between IPv4 and IPv6
- Understand what ports are and why they matter
- Trace the journey of data from your keyboard to a server and back

---

## Packets: Breaking Data Into Pieces

When you send a file, a message, or load a web page, the data doesn't travel as one continuous stream. It's broken into small chunks called **packets**.

### Why Packets?

- **Efficiency:** Multiple packets from different senders can share the same network links, interleaved with each other. If one large file owned the link entirely, everyone else would wait.
- **Resilience:** If one packet is lost or corrupted, only that packet needs to be re-sent — not the entire file.
- **Multiple paths:** Different packets can take different routes through the network and be reassembled at the destination.

> **Analogy:** Imagine sending a 500-page book to a friend across the country. You could try to mail it as one package, but if it gets lost, you have to resend everything. Instead, you tear out each page, put it in an envelope labeled "Page 47 of 500 — From: Alice — To: Bob," and mail all 500 envelopes separately. Each envelope might travel different routes. Bob reassembles the book in order when all pages arrive.

### What's in a Packet?

Every packet has two parts:

```
┌────────────────────────────────────────┐
│              HEADER                    │
│  Source IP | Destination IP | Protocol │
│  Port | Sequence number | Checksum     │
├────────────────────────────────────────┤
│              PAYLOAD                   │
│  (the actual data — part of your file, │
│   web page, message, etc.)             │
└────────────────────────────────────────┘
```

- **Header:** Routing information — where it came from, where it's going, what position it holds in the sequence
- **Payload:** The actual content
- **Typical size:** 1,500 bytes (1.5 KB) for Ethernet; often smaller

---

## The TCP/IP Model: Four Layers

Networks are built in **layers**. Each layer has a specific job and hands work off to the layer above or below it. This layered design means each layer can change independently without breaking the others.

The **TCP/IP model** (also called the Internet model) has four layers:

```
┌───────────────────────────────┐
│    4. APPLICATION LAYER       │  ← HTTP, FTP, DNS, SMTP
├───────────────────────────────┤
│    3. TRANSPORT LAYER         │  ← TCP, UDP
├───────────────────────────────┤
│    2. INTERNET LAYER          │  ← IP (routing, addresses)
├───────────────────────────────┤
│    1. NETWORK ACCESS LAYER    │  ← Ethernet, Wi-Fi, physical cables
└───────────────────────────────┘
```

> **Analogy:** Think of shipping a package internationally.
> - **Layer 4 (Application):** You write a letter and seal it in an envelope
> - **Layer 3 (Transport):** The postal service assigns it a tracking number, handles delivery confirmation, and re-sends if lost
> - **Layer 2 (Internet):** Addresses are standardized; routing decisions made at each post office
> - **Layer 1 (Network Access):** The physical truck, airplane, or ship carrying the package

### Layer 1: Network Access (Link Layer)

Handles the **physical transmission** of data on a single network segment:
- How bits are encoded as electrical signals, light, or radio waves
- Technologies: **Ethernet** (wired), **Wi-Fi** (802.11), **fiber optic**
- Deals with **MAC addresses** — hardware addresses burned into every network interface card (NIC) at the factory

A MAC address looks like: `00:1A:2B:3C:4D:5E` — 6 pairs of hex digits, globally unique per device.

### Layer 2: Internet Layer (Network Layer)

Handles **routing** — getting packets from source to destination across multiple networks:
- **IP (Internet Protocol)** operates here
- Every device on the Internet has an **IP address**
- Routers operate at this layer, reading destination IP addresses and forwarding packets toward their destination
- Does **not** guarantee delivery or order — it's "best effort"

### Layer 3: Transport Layer

Handles **communication between applications** on two devices:
- **TCP** — ensures reliable, ordered delivery
- **UDP** — fast but no delivery guarantee
- **Ports** — identify which application on a device should receive the packet

### Layer 4: Application Layer

The protocols that applications use directly:
- **HTTP/HTTPS** — web pages
- **FTP** — file transfer
- **SMTP/IMAP** — email
- **DNS** — name resolution
- **SSH** — secure remote access

Each layer **wraps** the layer above it in its own header. This process is called **encapsulation**:

```
Your message "Hello!"
  → Application adds HTTP headers         [HTTP | "Hello!"]
  → Transport adds TCP header              [TCP | HTTP | "Hello!"]
  → Internet adds IP header          [IP | TCP | HTTP | "Hello!"]
  → Network Access adds Ethernet header   [ETH | IP | TCP | HTTP | "Hello!"]
  ──────────── sent over wire ────────────
  → Receiver unwraps each layer in reverse
```

---

## IP Addresses

An **IP address** is a numerical label assigned to every device on a network. It's how the Internet knows where to send data.

### IPv4

IPv4 addresses are **32-bit numbers**, written as four groups of 0–255 separated by dots:

```
192.168.1.105
74.125.224.72      ← google.com
151.101.1.69       ← reddit.com
```

IPv4 can represent 2³² addresses = ~4.3 billion unique addresses. This seemed like plenty in the 1980s. It is not enough today.

**Special IPv4 addresses:**
- `127.0.0.1` — **loopback** (your own computer; "localhost")
- `192.168.x.x` — private network (your home/office LAN; not routable on the Internet)
- `10.x.x.x` — private network
- `172.16.x.x – 172.31.x.x` — private network

### IPv6

IPv6 addresses are **128-bit numbers**, written in hex:

```
2001:0db8:85a3:0000:0000:8a2e:0370:7334
```

IPv6 can represent 2¹²⁸ addresses — approximately 340 undecillion (340 followed by 36 zeros). Every grain of sand on Earth could have trillions of addresses. We will not run out.

IPv6 adoption is ongoing. Most modern devices and networks support both IPv4 and IPv6, with transition mechanisms bridging the gap.

### NAT: How IPv4 Hasn't Run Out Yet

With only 4.3 billion IPv4 addresses and 5+ billion Internet users, how does this work?

**NAT (Network Address Translation):** Your home router has one public IP address (assigned by your ISP). All devices in your home share it. The router tracks which internal device is communicating with which external server, and translates addresses in both directions.

```
[Your Laptop 192.168.1.5 ]  ┐
[Your Phone  192.168.1.6 ]  ├─── Router [Public IP: 203.0.113.10] ─── Internet
[Your TV     192.168.1.7 ]  ┘
```

From the Internet's perspective, all traffic from your home comes from `203.0.113.10`. Only the router knows which internal device is which.

---

## TCP vs UDP

The **Transport Layer** has two main protocols, each suited for different situations.

### TCP — Transmission Control Protocol

TCP provides **reliable, ordered, error-checked** delivery.

**How TCP works:**

1. **Three-way handshake** to establish a connection:
   ```
   Client → Server: SYN (synchronize — "I want to connect")
   Server → Client: SYN-ACK (synchronize-acknowledge — "OK, ready")
   Client → Server: ACK (acknowledge — "Great, let's go")
   ```
2. Data is sent in segments, each **numbered** (sequence numbers)
3. Receiver sends **acknowledgments (ACKs)** for each segment
4. If no ACK arrives, the sender **retransmits**
5. Receiver **reorders** segments that arrive out of order
6. **Four-way handshake** to close the connection cleanly

**TCP guarantees:**
- All data arrives (or the connection is reported as failed)
- Data arrives in the correct order
- No duplicates

**TCP is used for:**
- Web browsing (HTTP/HTTPS)
- Email (SMTP, IMAP)
- File transfers (FTP, SFTP)
- SSH
- Anything where losing data is unacceptable

**Cost of TCP:** The overhead of handshaking, acknowledgments, and retransmission adds latency.

### UDP — User Datagram Protocol

UDP is a **connectionless, fire-and-forget** protocol. No handshake, no acknowledgments, no retransmission.

**UDP characteristics:**
- No connection setup — just send
- No guarantees — packets may be lost, duplicated, or arrive out of order
- Very low overhead — minimal headers, no state maintained
- Fast

**UDP is used for:**
- **Video streaming** (Netflix, YouTube) — a dropped frame is better than stalling to wait for retransmission
- **Online gaming** — a missed position update is better than lag
- **VoIP/Video calls** (Zoom, FaceTime) — dropped audio for a millisecond is fine; waiting for retransmission creates jarring delays
- **DNS queries** — simple request/response; fast enough to just retry if no response
- **Live broadcasts** — can't wait for retransmission of past data

> **Analogy:** TCP is like certified mail with tracking and signature confirmation — guaranteed delivery, but more paperwork and slower. UDP is like shouting across a crowded room — fast and cheap, but some messages might not get through.

### Comparison

| Feature | TCP | UDP |
|---------|-----|-----|
| Connection | Required (3-way handshake) | None |
| Delivery guarantee | Yes | No |
| Order guarantee | Yes | No |
| Error detection | Yes | Basic checksum only |
| Speed | Slower | Faster |
| Overhead | Higher | Lower |
| Use cases | Web, email, files, SSH | Video, gaming, VoIP, DNS |

---

## Ports: Addressing Applications, Not Just Devices

An IP address identifies a **device**. A **port** identifies a specific **application or service** on that device.

Ports are 16-bit numbers from 0 to 65,535.

> **Analogy:** An IP address is like a building's street address. A port is like an apartment number. The postal worker (network) delivers to the building; the apartment number directs it to the right resident (application).

### Well-Known Ports (0–1023)

These are standardized — every OS reserves them for specific protocols:

| Port | Protocol | Used For |
|------|----------|---------|
| 20, 21 | FTP | File transfer |
| 22 | SSH | Secure remote shell |
| 23 | Telnet | Unsecured remote shell (legacy) |
| 25 | SMTP | Sending email |
| 53 | DNS | Domain name resolution |
| 80 | HTTP | Web (unencrypted) |
| 143 | IMAP | Receiving email |
| 443 | HTTPS | Web (encrypted) |
| 3306 | MySQL | Database |
| 5432 | PostgreSQL | Database |
| 8080 | HTTP alt | Often used for development servers |
| 27017 | MongoDB | Database |

When you type `https://google.com` in your browser, your browser automatically connects to port **443** on Google's server. You don't have to specify it — the protocol implies the port.

If you need a different port, you specify it explicitly: `http://localhost:3000` connects to port 3000 on your own machine.

---

## Routers, Switches, and Hubs

These devices form the physical infrastructure of networks.

### Hub (Legacy)

A hub is a **dumb repeater** — it receives a packet and broadcasts it to *every* device on the network. All devices see all traffic. Hubs are rare today; switches replaced them.

### Switch

A switch is **smarter** — it learns which MAC address is connected to which port, and delivers packets only to the intended recipient. This is much more efficient. Most LANs use switches.

### Router

A router operates at the **IP layer** — it reads destination IP addresses and decides which network to forward packets toward. Routers connect different networks together. Your home router connects your LAN to your ISP's network; large routers in the Internet backbone make decisions about routing packets across continents.

**Home router = router + switch + Wi-Fi access point + NAT**, all in one box.

---

## A Packet's Journey: Browser to Google.com

Let's trace what happens when you type `http://google.com` and press Enter:

```
1. DNS LOOKUP
   Your browser asks the OS: "What is the IP address of google.com?"
   OS checks its DNS cache → not found
   OS sends a UDP packet to your DNS server (port 53)
   DNS server responds: "google.com = 142.250.80.46"

2. TCP HANDSHAKE
   Your browser initiates a TCP connection to 142.250.80.46:80
   SYN → SYN-ACK → ACK
   Connection established

3. HTTP REQUEST (Application Layer)
   Browser sends: "GET / HTTP/1.1\r\nHost: google.com\r\n\r\n"
   This is wrapped in TCP → IP → Ethernet headers (encapsulation)

4. ROUTING
   Packet leaves your computer via Ethernet/Wi-Fi
   Reaches your home router → NAT translates your private IP to your public IP
   Router forwards to your ISP
   ISP routers forward toward Google's network
   (Typically 10-20 hops through various routers worldwide)

5. SERVER RECEIVES REQUEST
   Google's server unwraps the packet layers
   Reads the HTTP GET request
   Prepares an HTML response

6. RESPONSE RETURNS
   Response broken into packets
   Each packet travels back through the network
   TCP reassembles them in order on your machine

7. BROWSER RENDERS
   Browser receives HTML, CSS, JavaScript
   Builds the DOM, applies styles, executes scripts
   Displays the Google homepage
```

The entire journey described above takes **20–200 milliseconds** depending on your location.

---

## The OSI Model (A Note)

You may encounter the **OSI model** (Open Systems Interconnection) — a 7-layer model used heavily in networking education and certifications:

| Layer | Name | Example |
|-------|------|---------|
| 7 | Application | HTTP, FTP, DNS |
| 6 | Presentation | TLS/SSL, JPEG encoding |
| 5 | Session | Session establishment |
| 4 | Transport | TCP, UDP |
| 3 | Network | IP |
| 2 | Data Link | Ethernet, Wi-Fi |
| 1 | Physical | Cables, radio waves |

The TCP/IP model collapses some OSI layers together. In practice, both models are used — TCP/IP for Internet protocols, OSI for discussing concepts. When someone says "this is a Layer 3 problem," they mean the network/routing layer (IP).

**Further Reading:**
- [How the Internet Works — Stanford (Visual)](https://web.stanford.edu/class/msande91si/www-spr04/readings/week1/InternetWhitepaper.htm)
- [TCP/IP Explained — GeeksforGeeks](https://www.geeksforgeeks.org/tcp-ip-model/)
- [Packet Switching — Computerphile (YouTube)](https://www.youtube.com/watch?v=AYdF7b3nMto)
- [TCP vs UDP — Cloudflare](https://www.cloudflare.com/learning/ddos/glossary/user-datagram-protocol-udp/)
- [IP Addressing — Cisco](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html)

---

## Check Your Understanding

1. Why is data broken into packets instead of sent as one continuous stream?
2. What is the difference between an IP address and a port number?
3. You are downloading a large file. Should it use TCP or UDP? Why?
4. You are in a video call. Should it use TCP or UDP? Why?
5. Your computer has IP address `192.168.1.50`. Is this a public or private IP address? What does that mean?
6. What is NAT, and why does it exist?

---

*Previous: [01 — What Is a Network?](01-what-is-a-network.md)*
*Next: [03 — Application Layer Protocols](03-application-layer-protocols.md)*
