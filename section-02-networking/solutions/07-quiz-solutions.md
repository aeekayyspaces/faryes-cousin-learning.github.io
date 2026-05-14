# Solutions — 07 Section Quiz

Answer key for the quiz in [07 — Quiz and Checkpoints](../07-quiz-and-checkpoints.md).

---

## Part 1: Multiple Choice — Answer Key

| # | Answer | Explanation |
|---|--------|-------------|
| 1 | **B — Resilient communication network with no single point of failure** | ARPANET was funded by ARPA partly because of Cold War concerns about centralized communication networks being vulnerable to attack. The packet-switching design meant messages could find alternate routes if parts of the network were destroyed. |
| 2 | **C — Multiple data streams can share network links efficiently and route around failures** | Circuit switching reserves a dedicated path for the duration of a call. Packet switching sends small independent chunks that share links with other traffic, making far more efficient use of network capacity, and automatically route around failed nodes. |
| 3 | **C — Internet Layer (IP)** | The Internet (Network) Layer is where IP operates. IP addresses are at this layer, and routers read destination IP addresses to forward packets toward their destination. The Transport Layer handles end-to-end communication between applications; the Application Layer is for protocols like HTTP. |
| 4 | **B — Private IP address; not directly reachable from the public Internet** | `192.168.x.x` is a reserved private address range. Your home router has one public IP; all devices behind it share it via NAT. External servers cannot initiate connections to `192.168.1.45` directly. |
| 5 | **B — UDP — because low latency matters more than guaranteed delivery** | In real-time games, a missed position update is better than a 200ms delay caused by TCP retransmission. A player would rather see a brief glitch than stutter; by the time a retransmit arrived, the data would be outdated anyway. |
| 6 | **C — Resource not found at the requested URL** | `404 Not Found` is the client error meaning the server cannot find anything at the requested path. `200` = success; `401` = unauthorized (need to log in); `500` = server error. |
| 7 | **C — Device broadcasts a request; DHCP server offers an available address** | The DORA process: Discover (broadcast) → Offer (server offers IP) → Request (client accepts) → Acknowledge (server confirms). The device has no IP yet when it starts, so it broadcasts to the entire local network. |
| 8 | **B — Checks local cache first, then queries a resolver, which recursively finds the answer** | Browsers cache DNS, the OS caches DNS, and your configured DNS resolver caches most answers. Only on a true cache miss does the resolver recursively query root servers → TLD servers → authoritative servers. Most lookups are served from cache. |
| 9 | **B — Napster required a central index server; BitTorrent can operate without any central server via DHT** | Napster used P2P file transfer but centralized search/discovery — making it legally and technically vulnerable. BitTorrent with DHT is fully decentralized: peers find each other without any central server. |
| 10 | **A — Enables verbose mode — shows full request and response headers** | `-v` shows the complete HTTP conversation: TLS negotiation details, request headers (prefixed with `>`), and response headers (prefixed with `<`). This is what browsers hide from you. |
| 11 | **C — HTTPS uses TLS encryption; telnet sends plaintext and cannot perform the TLS handshake** | TLS requires a cryptographic handshake involving key exchange, cipher negotiation, and certificate verification. Telnet just sends raw bytes — it has no ability to perform this handshake, so connecting to port 443 would produce encrypted garbage. |
| 12 | **B — Symmetric uses the same key to encrypt and decrypt; asymmetric uses a public key to encrypt and a private key to decrypt** | The critical property of asymmetric encryption is the key pair: public key is freely shared (used to encrypt), private key is secret (used to decrypt). This solves the key exchange problem. |
| 13 | **B — The server's identity has been verified by a trusted Certificate Authority** | A certificate proves that a trusted CA verified the server controls the domain listed in the certificate. It does NOT mean the website is trustworthy, safe, or legitimate — a phishing site can also have a valid certificate. |
| 14 | **C — The certificate has passed its validity end date and hasn't been renewed** | Certificate expiry is the most common certificate error. Every certificate has a `notAfter` date; when that date passes, the certificate is no longer considered valid. Note: answer B (wrong clock) is also plausible and worth checking, but `ERR_CERT_EXPIRED` most commonly means the server's cert actually expired. |
| 15 | **B — Opens a listener on port 9999, waiting for an incoming TCP connection** | `nc -l 9999` puts netcat in listen mode on port 9999. Nothing happens until another process connects to that port. Once connected, stdin/stdout of both processes are bridged. |

---

## Part 2: Short Answer — Suggested Responses

### 1. TCP vs UDP analogy with examples

**TCP** is like certified mail with delivery confirmation. You know your message arrived because the recipient must sign for it (ACK). If the delivery fails, the postal service retries. This adds overhead but guarantees delivery.

**UDP** is like shouting something in a crowded room. Fast and no overhead — but some listeners might miss it. There's no retry mechanism; you just shout again if needed.

**TCP example:** Downloading a file — every byte must arrive correctly, in order, or the file is corrupted.

**UDP example:** A video call — if one audio frame is dropped, you want the next frame immediately. Waiting for a retransmission would cause jarring delays; the brief audio gap is less disruptive.

---

### 2. Complete journey: `https://google.com`

1. **Input:** Keyboard sends keystrokes → OS → browser receives URL
2. **DNS lookup:** Browser checks its DNS cache → OS checks its cache → OS queries configured DNS resolver (e.g., `8.8.8.8`) → resolver finds `google.com = 142.250.80.46` via recursive query → result cached
3. **TCP handshake:** Browser initiates TCP connection to `142.250.80.46:443` → SYN → SYN-ACK → ACK
4. **TLS handshake:** Browser and Google's server negotiate cipher suite, Google presents certificate, browser verifies it chains to a trusted CA and matches `google.com`, both sides derive session keys
5. **HTTP request:** Browser sends encrypted `GET / HTTP/1.1` with headers; NAT translates source IP at your router; packet traverses ISP, backbone routers, and Google's network
6. **Server response:** Google's server returns encrypted HTML, CSS, JavaScript in multiple TCP segments
7. **Browser rendering:** TCP reassembles segments → browser parses HTML builds DOM → CSS applied → JS executed → GPU renders pixels → monitor displays Google homepage
8. **Total time:** Typically 50–300ms

---

### 3. NAT — what it is and why it exists

**NAT (Network Address Translation)** is a technique where a router translates private IP addresses (like `192.168.1.x`) to a single public IP address when sending traffic to the Internet, and translates back on the return.

It exists because IPv4 only supports ~4.3 billion addresses — not enough for every device on Earth. NAT allows thousands of devices to share a single public IP. Your home router gives every device a private IP, and all traffic to the Internet appears to come from one address (your router's public IP).

Side effects: devices behind NAT cannot easily receive inbound connections (which is why gaming lobbies and P2P applications need special techniques like STUN or port forwarding).

---

### 4. DHT without a central server

A DHT distributes the responsibility for storing and finding data across all participating peers. Every peer is assigned a random node ID. Data keys (like "who has this torrent?") are mapped to the peer whose ID is numerically closest to the key.

To find data: you ask a peer you know, they check if they're closest to the key or route you toward a peer that is, and so on. Each step gets you closer. In a well-populated DHT, you find any data within a small number of hops (logarithmic in the total number of peers).

When peers leave or join, the data they held is redistributed to their numeric neighbors. The network is self-healing — no central coordinator needed.

---

### 5. TLS handshake in plain language

The TLS handshake is the "setup conversation" that happens before any encrypted HTTP data is exchanged.

First, the client and server agree on which encryption algorithms to use (cipher suite negotiation). Then the server proves its identity by presenting a certificate — a document signed by a trusted Certificate Authority that proves "this key pair belongs to example.com."

Next comes **key exchange**: both sides use asymmetric cryptography (like ECDH) to safely agree on a shared secret over the insecure network, without ever transmitting that secret directly. This solves the key exchange problem: an eavesdropper who intercepts the handshake cannot derive the shared secret.

Once both sides have the shared secret, they derive **session keys** for symmetric encryption (like AES). They switch to symmetric for the rest of the connection because it's 1000x faster than asymmetric. All subsequent HTTP data is encrypted with these session keys.

In short: **asymmetric to share the secret safely, then symmetric for fast bulk encryption.**

---

*Back to quiz: [07 — Quiz and Checkpoints](../07-quiz-and-checkpoints.md)*
