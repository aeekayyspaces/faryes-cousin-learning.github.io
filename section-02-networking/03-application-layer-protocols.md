# 03 — Application Layer Protocols: HTTP, HTTPS, FTP, DHCP, DNS, and More

## Learning Objectives

By the end of this page, you will be able to:

- Explain what a protocol is and why protocols matter
- Describe HTTP's request/response model, methods, and status codes
- Explain what HTTPS adds to HTTP
- Understand what FTP is used for
- Explain how DHCP assigns IP addresses automatically
- Describe how DNS resolves domain names to IP addresses
- Name other key protocols (SMTP, SSH) and their purposes

---

## What Is a Protocol?

A **protocol** is a set of rules that defines how two parties communicate. Both sides must agree on the same protocol for communication to work.

> **Analogy:** When you call someone on the phone, you both follow a protocol without thinking about it: one person says "Hello," the other responds, you take turns speaking, you say "Goodbye" to signal the call is ending. If one person followed completely different rules — say, by only communicating in Morse code — the call would fail. Computer protocols work the same way: both sides must speak the same language, in the same format, following the same rules.

Protocols define:
- The **format** of messages (what fields are included, in what order)
- The **sequence** of messages (who speaks first, what triggers what response)
- **Error handling** (what to do when something goes wrong)
- **Connection management** (how to open and close a communication)

---

## HTTP — HyperText Transfer Protocol

**HTTP** is the protocol that powers the World Wide Web. When your browser loads a web page, it uses HTTP to request and receive the page's content.

HTTP is a **request/response** protocol:
1. The **client** (your browser) sends a request
2. The **server** (Google, Netflix, etc.) sends a response
3. The connection may be reused for additional requests

### HTTP Requests

Every HTTP request has:

```
METHOD /path HTTP/version
Header: value
Header: value
[blank line]
[optional body]
```

A real example — your browser loading `http://example.com/about`:
```
GET /about HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15)
Accept: text/html,application/xhtml+xml
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
```

### HTTP Methods

The **method** tells the server what action to perform:

| Method | Purpose | Body? | Example |
|--------|---------|-------|---------|
| **GET** | Retrieve a resource | No | Load a web page, fetch an image |
| **POST** | Submit data to create something | Yes | Submit a form, create a user account |
| **PUT** | Replace a resource entirely | Yes | Update a user profile |
| **PATCH** | Partially update a resource | Yes | Change just a user's email |
| **DELETE** | Remove a resource | No | Delete a post |
| **HEAD** | Like GET but no response body | No | Check if a resource exists |
| **OPTIONS** | List allowed methods | No | CORS preflight checks |

> **Key Point:** GET requests should never change server state — they're for *reading*. POST, PUT, PATCH, DELETE are for *writing or modifying*. This distinction matters for caching, bookmarking, and security.

### HTTP Responses

Every HTTP response has:

```
HTTP/version STATUS_CODE STATUS_TEXT
Header: value
[blank line]
[body — the actual content]
```

A real example:
```
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Length: 1256
Date: Tue, 06 May 2026 10:00:00 GMT

<!DOCTYPE html>
<html>...
```

### HTTP Status Codes

Status codes tell the client what happened. They're grouped by their first digit:

| Range | Meaning | Examples |
|-------|---------|---------|
| **1xx** | Informational | `100 Continue` |
| **2xx** | Success | `200 OK`, `201 Created`, `204 No Content` |
| **3xx** | Redirection | `301 Moved Permanently`, `302 Found`, `304 Not Modified` |
| **4xx** | Client error | `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found` |
| **5xx** | Server error | `500 Internal Server Error`, `502 Bad Gateway`, `503 Service Unavailable` |

**The most famous ones:**
- `200 OK` — everything worked
- `301 Moved Permanently` — this URL has moved; update your bookmarks
- `404 Not Found` — the page doesn't exist at this URL
- `401 Unauthorized` — you need to log in
- `403 Forbidden` — you're logged in but don't have permission
- `500 Internal Server Error` — something broke on the server
- `503 Service Unavailable` — server is overloaded or down for maintenance

### HTTP Headers

Headers are key-value pairs that carry metadata about the request or response:

**Common request headers:**
- `Host: example.com` — which website you want (one server can host many)
- `User-Agent: Mozilla/5.0...` — identifies the client (browser, bot, etc.)
- `Authorization: Bearer abc123` — authentication token
- `Content-Type: application/json` — format of the request body
- `Cookie: session=xyz` — sends stored cookies to the server

**Common response headers:**
- `Content-Type: text/html` — format of the response body
- `Content-Length: 1256` — size of the body in bytes
- `Set-Cookie: session=abc; HttpOnly` — instructs the browser to store a cookie
- `Cache-Control: max-age=3600` — how long the client can cache this response
- `Location: /new-url` — used with 3xx redirects

### HTTP Versions

| Version | Year | Key Feature |
|---------|------|-------------|
| HTTP/1.0 | 1996 | One request per connection |
| HTTP/1.1 | 1997 | Persistent connections, chunked transfer |
| HTTP/2 | 2015 | Binary, multiplexed (multiple requests in parallel on one connection), header compression |
| HTTP/3 | 2022 | Built on QUIC (UDP-based), faster connection setup, better mobile performance |

Most modern websites use HTTP/2 or HTTP/3. Your browser negotiates the version automatically.

**Further Reading:**
- [HTTP — MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/HTTP)
- [HTTP Status Codes — httpstatuses.com](https://httpstatuses.com/)
- [HTTP/2 Explained — Cloudflare](https://www.cloudflare.com/learning/performance/http2-vs-http1.1/)

---

## HTTPS — HTTP Secure

**HTTPS** is HTTP with **encryption** layered on top using **TLS (Transport Layer Security)**.

Without HTTPS:
- Every router and ISP between you and the server can read your traffic
- Attackers on the same Wi-Fi network can see your passwords, messages, and data
- Attackers can modify the content you receive (**man-in-the-middle attack**)

With HTTPS:
- All traffic between your browser and the server is **encrypted**
- No one in the middle can read or modify it
- Your browser verifies the server's identity using a **digital certificate**

We'll cover encryption and TLS in detail in [06 — Security and Encryption](06-security-and-encryption.md).

**How to spot HTTPS:**
- URL starts with `https://`
- A padlock icon appears in the browser address bar
- Clicking the padlock shows the certificate details

> **Key Point:** In 2024, ~95% of all web traffic uses HTTPS. Any website that still uses plain HTTP for forms, logins, or sensitive data is dangerously insecure. Browsers now actively warn users about HTTP sites.

---

## FTP — File Transfer Protocol

**FTP** (File Transfer Protocol) was designed in 1971 for transferring files between computers. It predates the web.

### How FTP Works

FTP uses **two connections**:
- **Control connection (port 21):** Commands and responses
- **Data connection (port 20 or dynamic):** Actual file data

A typical session:
```
Client → Server: Connect to port 21
Server → Client: "220 Welcome to FTP server"
Client → Server: USER myusername
Server → Client: "331 Password required"
Client → Server: PASS mypassword
Server → Client: "230 Login successful"
Client → Server: RETR /files/document.pdf
Server → Client: (opens data connection, sends file)
```

### FTP Problems

- **No encryption** — username, password, and files all travel in plaintext
- Replaced in modern use by:
  - **SFTP** (SSH File Transfer Protocol) — encrypted, uses SSH
  - **FTPS** (FTP Secure) — FTP with TLS encryption
  - **SCP** (Secure Copy Protocol) — simple encrypted file copy
  - **HTTPS file uploads** — web-based uploads

FTP is still found in legacy systems, some hosting environments, and industrial equipment. New systems should use SFTP or HTTPS.

---

## DHCP — Dynamic Host Configuration Protocol

When you connect your laptop to a new Wi-Fi network, how does it automatically get an IP address, DNS server address, and network settings — without you configuring anything?

**DHCP** (Dynamic Host Configuration Protocol) handles this automatically.

### The DHCP Process (DORA)

```
1. DISCOVER
   New device broadcasts: "I need an IP address! Anyone there?"
   (Sent to 255.255.255.255 — the broadcast address — since it has no IP yet)

2. OFFER
   DHCP server (usually your router) responds:
   "I'm offering you 192.168.1.105 for 24 hours"

3. REQUEST
   Device responds: "I'll take 192.168.1.105, please"

4. ACKNOWLEDGE
   Server: "Confirmed. Here are your settings:"
   - IP address: 192.168.1.105
   - Subnet mask: 255.255.255.0
   - Default gateway: 192.168.1.1 (your router)
   - DNS server: 8.8.8.8 (Google's DNS)
   - Lease duration: 24 hours
```

The IP address is **leased** — after the lease expires, the device must renew it. This is why your IP address may change occasionally on home networks.

> **Analogy:** DHCP is like a hotel check-in. You arrive (connect to Wi-Fi), the front desk (DHCP server) assigns you a room (IP address) for a certain number of nights (lease time). When you check out (disconnect), the room becomes available for someone else.

---

## DNS — Domain Name System

**DNS** (Domain Name System) translates human-readable domain names like `google.com` into IP addresses like `142.250.80.46`.

Without DNS, you'd need to memorize the IP address of every website you visit.

### The DNS Hierarchy

DNS is a distributed, hierarchical database — no single server knows all domain names. Instead, resolution is delegated:

```
Root DNS Servers (13 clusters globally)
    └── TLD Servers (.com, .org, .net, .uk, etc.)
            └── Authoritative Nameservers (hold records for specific domains)
```

### A DNS Query Step by Step

When your browser needs to resolve `blog.example.com`:

```
1. Browser checks its local DNS cache → not found

2. Browser asks the OS → OS checks its cache → not found

3. OS asks the configured DNS resolver (usually your router or ISP's DNS)
   Common public resolvers: 8.8.8.8 (Google), 1.1.1.1 (Cloudflare)

4. Resolver checks its cache → not found (first time)

5. Resolver asks a Root DNS Server: "Who handles .com?"
   Root Server: "The .com TLD servers are at these addresses"

6. Resolver asks a .com TLD Server: "Who handles example.com?"
   TLD Server: "The authoritative nameserver for example.com is ns1.example.com"

7. Resolver asks ns1.example.com: "What is blog.example.com?"
   Authoritative Server: "blog.example.com = 93.184.216.34"

8. Resolver caches the answer and returns it to your OS
   Your OS caches it and returns it to your browser
   Browser caches it and makes the HTTP connection

Total time: typically 20–120ms on first lookup; near-instant from cache
```

### DNS Record Types

| Record | Purpose | Example |
|--------|---------|---------|
| **A** | Maps name to IPv4 address | `google.com → 142.250.80.46` |
| **AAAA** | Maps name to IPv6 address | `google.com → 2607:f8b0:4004:c08::8a` |
| **CNAME** | Alias — maps one name to another | `www.example.com → example.com` |
| **MX** | Mail server for a domain | `example.com → mail.example.com` |
| **TXT** | Text data (verification, SPF, etc.) | Domain ownership verification |
| **NS** | Nameservers for a domain | Which servers are authoritative |
| **PTR** | Reverse lookup (IP → name) | Used by email servers |

### TTL — Time to Live

Every DNS record has a **TTL (Time to Live)** value — how long the record can be cached before it must be re-queried. Short TTLs (60s) mean changes propagate quickly; long TTLs (86400s = 24 hours) reduce DNS server load.

When companies "change their DNS" (like pointing a domain to a new server), they must wait for old cached records to expire — which is why DNS changes can take up to 48 hours to fully propagate worldwide.

---

## SMTP, IMAP, POP3 — Email Protocols

Email involves multiple protocols working together:

| Protocol | Port | Role |
|----------|------|------|
| **SMTP** (Simple Mail Transfer Protocol) | 25, 587, 465 | **Sending** email — from your client to your mail server, and between mail servers |
| **IMAP** (Internet Message Access Protocol) | 143, 993 | **Receiving** email — syncs messages between server and clients; messages stay on server |
| **POP3** (Post Office Protocol) | 110, 995 | **Receiving** email (older) — downloads messages to local device; removes from server |

When you click "Send" in Gmail:
```
Your browser → Gmail SMTP servers → Recipient's mail server → Recipient's inbox
```

IMAP is now standard for receiving; POP3 is mostly legacy.

---

## SSH — Secure Shell

**SSH** (Secure Shell) provides an encrypted terminal session to a remote computer.

Before SSH, **Telnet** (port 23) was used — but Telnet sends everything in plaintext, including passwords. SSH (port 22) replaced it with full encryption.

```bash
# Connect to a remote server
ssh username@server-ip-or-hostname

# Example: connect to a web server
ssh admin@203.0.113.50

# Connect with a specific key file
ssh -i ~/.ssh/mykey.pem ubuntu@203.0.113.50
```

SSH is essential for:
- Logging into Linux servers remotely
- Deploying web applications
- Secure file transfers (SFTP)
- Tunneling other protocols through an encrypted channel

---

## Quick Protocol Reference

| Protocol | Port | Transport | Encrypted? | Used For |
|----------|------|-----------|------------|---------|
| HTTP | 80 | TCP | No | Web pages |
| HTTPS | 443 | TCP | Yes (TLS) | Secure web pages |
| FTP | 20/21 | TCP | No | File transfer (legacy) |
| SFTP | 22 | TCP | Yes (SSH) | Secure file transfer |
| SSH | 22 | TCP | Yes | Secure remote shell |
| Telnet | 23 | TCP | No | Remote shell (legacy) |
| SMTP | 25/587 | TCP | Optional | Sending email |
| DNS | 53 | UDP/TCP | Optional | Name resolution |
| DHCP | 67/68 | UDP | No | IP address assignment |
| IMAP | 143/993 | TCP | Optional/Yes | Receiving email |
| POP3 | 110/995 | TCP | Optional/Yes | Receiving email (legacy) |

---

## Check Your Understanding

1. What is the difference between a `GET` and a `POST` request?
2. You try to access a page on a website and see `403 Forbidden`. What does this mean?
3. Why is FTP considered insecure, and what should be used instead?
4. Walk through the DHCP DORA process. What problem does each step solve?
5. Why doesn't your browser have to contact a root DNS server every time you visit a website?
6. What is the difference between SMTP and IMAP?

---

*Previous: [02 — How Data Travels](02-how-data-travels.md)*
*Next: [04 — P2P and Decentralized Networks](04-p2p-networks.md)*
