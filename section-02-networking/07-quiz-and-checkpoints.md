# 07 — Section Quiz and Checkpoints

## Section 2 Learning Objectives Review

Before taking this quiz, you should be able to:

- [ ] Explain what a network is and describe LAN, WAN, and the Internet
- [ ] Describe the TCP/IP model's four layers and what each does
- [ ] Distinguish between TCP and UDP and know when each is appropriate
- [ ] Explain IP addresses, ports, and NAT
- [ ] Describe key protocols: HTTP, HTTPS, FTP, DHCP, DNS
- [ ] Explain the difference between client-server and P2P architectures
- [ ] Use `curl` to make HTTP requests and inspect responses
- [ ] Use `telnet` to connect to a server and send raw HTTP commands
- [ ] Use `nc` (netcat) to create a direct socket connection
- [ ] Explain symmetric and asymmetric encryption
- [ ] Describe what TLS certificates are and why they matter
- [ ] Use `openssl` to inspect a server's certificate

---

## Part 1: Multiple Choice

Choose the best answer. Answer key in [solutions/07-quiz-solutions.md](solutions/07-quiz-solutions.md).

---

**1.** ARPANET was originally funded to solve which problem?

- A) Connect universities to IBM mainframes
- B) Create a resilient communication network with no single point of failure
- C) Provide commercial email services to government agencies
- D) Replace the telephone network with a digital alternative

---

**2.** What is the main advantage of packet switching over circuit switching?

- A) It creates a dedicated connection with guaranteed bandwidth
- B) It is simpler to implement in hardware
- C) Multiple data streams can share network links efficiently and route around failures
- D) It reduces the number of routers needed in a network

---

**3.** Which TCP/IP layer is responsible for routing packets between networks?

- A) Application Layer
- B) Transport Layer
- C) Internet Layer (IP)
- D) Network Access Layer

---

**4.** Your home Wi-Fi gives your laptop the IP address `192.168.1.45`. Which of the following is true?

- A) This is a public IP address; any computer on the Internet can connect to it directly
- B) This is a private IP address; it is not directly reachable from the public Internet
- C) This is an IPv6 address in compressed notation
- D) This is a loopback address; it only refers to your own computer

---

**5.** You are building a real-time multiplayer game where a dropped position update is acceptable but latency must be minimal. Which transport protocol should you use?

- A) TCP — because data integrity is important in games
- B) UDP — because low latency matters more than guaranteed delivery
- C) FTP — because it's designed for fast data transfer
- D) HTTP — because games use web servers

---

**6.** What does the HTTP status code `404` mean?

- A) The request succeeded and the resource was returned
- B) The client needs to log in before accessing this resource
- C) The resource was not found at the requested URL
- D) The server encountered an internal error

---

**7.** How does DHCP provide an IP address to a new device?

- A) The device checks a central database of assigned addresses
- B) The device generates a random address and checks if it's in use
- C) The device broadcasts a request; a DHCP server offers an available address
- D) The network administrator manually configures the address in a config file

---

**8.** When your browser resolves `github.com` to an IP address, it:

- A) Always queries a root DNS server directly
- B) Checks its local cache first, then queries a resolver, which recursively finds the answer
- C) Sends the domain name in the HTTP request and the server provides its own IP
- D) Uses ARP (Address Resolution Protocol) to map the name to an address

---

**9.** What is the fundamental difference between Napster and BitTorrent's architectures?

- A) Napster used UDP while BitTorrent uses TCP
- B) Napster required a central index server; BitTorrent can operate without any central server via DHT
- C) Napster was encrypted; BitTorrent is unencrypted
- D) Napster used HTTP; BitTorrent uses FTP

---

**10.** What does the `curl -v` flag do?

- A) Enables verbose mode — shows full request and response headers
- B) Increases download speed by enabling parallel connections
- C) Validates the SSL certificate and shows the certificate chain
- D) Sends the request to a verification proxy

---

**11.** Why can't you use telnet to manually type an HTTPS (port 443) request?

- A) Telnet doesn't support port numbers above 100
- B) HTTPS requires authentication before accepting any data
- C) HTTPS traffic is encrypted with TLS; telnet sends plaintext and can't perform the TLS handshake
- D) Port 443 is blocked by most firewalls

---

**12.** What is the difference between symmetric and asymmetric encryption?

- A) Symmetric uses software encryption; asymmetric uses hardware
- B) Symmetric uses the same key to encrypt and decrypt; asymmetric uses a public key to encrypt and a private key to decrypt
- C) Symmetric is used for certificates; asymmetric is used for data
- D) Symmetric is reversible; asymmetric is one-way (like a hash)

---

**13.** What does a TLS certificate prove?

- A) That the website's content is safe and the organization is legitimate
- B) That the server's identity has been verified by a trusted Certificate Authority
- C) That the connection is using the fastest available encryption algorithm
- D) That your browser is running the latest version

---

**14.** You visit `https://mybank.com` and see a `NET::ERR_CERT_EXPIRED` error. What has happened?

- A) The bank's server is down for maintenance
- B) Your computer's clock is wrong and needs to be updated
- C) The bank's TLS certificate has passed its validity end date and hasn't been renewed
- D) Your browser doesn't support the encryption algorithm the bank uses

---

**15.** The `netcat -l 9999` command:

- A) Lists all processes listening on port 9999
- B) Opens a listener on port 9999, waiting for an incoming TCP connection
- C) Sends a test packet to port 9999 to check if it's open
- D) Logs all traffic passing through port 9999

---

## Part 2: Short Answer

Answer in 2–4 sentences each. Suggested answers in [solutions/07-quiz-solutions.md](solutions/07-quiz-solutions.md).

**1.** Explain the difference between TCP and UDP using a real-world analogy. Give one example of an application that should use each.

**2.** Walk through what happens when you type `https://google.com` in your browser and press Enter. Start from your keyboard and end at the pixels appearing on your screen.

**3.** What is NAT, and why does it exist? What problem does it solve?

**4.** Explain how a DHT (Distributed Hash Table) allows peers to find each other without a central server.

**5.** Explain the TLS handshake in plain language. Why does it use asymmetric encryption at first and then switch to symmetric?

---

## Part 3: Code Challenges

Solutions in [solutions/05-networking-solutions.md](solutions/05-networking-solutions.md).

---

### Challenge 1: curl Exploration

**Goal:** Use curl to investigate a web server.

1. Make a `GET` request to `https://httpbin.org/get` with verbose output. Save the output to a file called `curl-investigation.txt`.
2. Find and record:
   - The HTTP version used
   - Your public IP address (shown in the "origin" field)
   - The `User-Agent` curl sent
   - The HTTP status code of the response
3. Now make the same request adding a custom header: `X-My-Name: YourNameHere`. Confirm the server received it.

---

### Challenge 2: HTTP Methods

**Goal:** Use curl to test different HTTP methods.

1. Send a `GET` request to `https://httpbin.org/anything` and record the response.
2. Send a `POST` request to `https://httpbin.org/anything` with a JSON body: `{"message": "Hello from curl", "section": 2}`
3. Compare the responses. What's different?
4. Send a `DELETE` request to `https://httpbin.org/anything`. What status code do you get? What does that tell you about httpbin's behavior?

---

### Challenge 3: Certificate Inspector

**Goal:** Use openssl to examine a certificate.

1. Connect to `github.com:443` using `openssl s_client` and capture the output.
2. Find and record:
   - The domain names covered by the certificate (Subject Alternative Names)
   - The Certificate Authority that issued it
   - The expiry date
3. Check the certificate of `expired.badssl.com` — what error do you see?
4. Try `curl https://expired.badssl.com/` — what does curl say?

---

### Challenge 4: The P2P Chat

**Goal:** Demonstrate a direct peer-to-peer connection using netcat.

1. Open two terminal windows side by side.
2. In Terminal 1, start a listener on port `7777`.
3. In Terminal 2, connect to it.
4. Send at least 3 messages from each terminal (alternating).
5. Record the commands you used and describe what you observed.

**Bonus:** Use netcat to transfer a small file between the two terminals.

---

### Challenge 5: DNS Investigation

**Goal:** Investigate DNS records using command-line tools.

**macOS/Linux:**
```bash
dig google.com          # full DNS query output
dig google.com A        # just A records (IPv4)
dig google.com MX       # mail server records
nslookup google.com     # simpler output
```

**Windows:**
```powershell
Resolve-DnsName google.com
Resolve-DnsName google.com -Type MX
nslookup google.com
```

Using these tools:
1. Find the IP address(es) for `github.com`.
2. Find the mail server(s) for `google.com`.
3. Find the nameservers for `cloudflare.com`.
4. How many IP addresses does `google.com` resolve to? Why might a major website have multiple IPs?

---

## Completion Checklist

When you've completed this section, you should be able to say "yes" to all:

- [ ] I can explain why packet switching makes the Internet resilient
- [ ] I understand the difference between TCP (reliable) and UDP (fast)
- [ ] I can explain how DNS resolves a domain name step by step
- [ ] I know what DHCP does and why it matters
- [ ] I can use `curl` to make GET and POST requests and inspect headers
- [ ] I can use `telnet` to make a raw HTTP request
- [ ] I can create a direct peer-to-peer connection using `nc`
- [ ] I understand why TLS/HTTPS is essential and how certificates work
- [ ] I can use `openssl` to inspect a server's certificate
- [ ] I completed all five code challenges

---

*Ready for the next section? → [Section 3: How the Web Works](../section-03-how-the-web-works/01-dns-and-domain-names.md)*

*Review sources: [08 — Sources](08-sources.md)*
