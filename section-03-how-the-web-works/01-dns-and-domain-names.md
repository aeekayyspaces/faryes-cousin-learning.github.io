# 01 — DNS and Domain Names: How an Address Becomes a Server

## Learning Objectives

By the end of this page, you will be able to:

- Explain the structure of a domain name (TLD, SLD, subdomain)
- Describe what ICANN is and why it exists
- Walk through how a browser resolves a domain name to an IP address
- Identify common DNS record types and what each does
- Explain what a CDN is and how GeoDNS works
- Understand TTL and why DNS changes take time to propagate

---

## The Problem DNS Solves

Every device on the Internet has a numeric IP address — like `140.82.113.4` for GitHub. Humans are terrible at remembering numbers like that. We're much better at remembering names like `github.com`.

**DNS (Domain Name System)** is the Internet's phone book — a global, distributed system that translates human-readable names into the IP addresses computers use to route traffic.

> **Analogy:** Before smartphones, people memorized a few phone numbers but relied on a phone book for everyone else. You'd look up "Pizza Palace" and find their number. DNS does exactly this — but automatically, in milliseconds, every time you visit a website.

---

## Anatomy of a Domain Name

A domain name reads right-to-left in terms of hierarchy:

```
      subdomain . second-level-domain . top-level-domain
         ↓               ↓                    ↓
        blog        .  example         .     com

      more specific ←————————————— more general
```

### Top-Level Domains (TLDs)

The rightmost part of a domain. There are several types:

| Type | Examples | Purpose |
|------|----------|---------|
| **Generic (gTLD)** | `.com`, `.org`, `.net`, `.edu`, `.gov`, `.io`, `.dev` | General use |
| **Country Code (ccTLD)** | `.uk`, `.de`, `.jp`, `.ca`, `.au`, `.io` | Country-specific |
| **New gTLDs** (2013+) | `.app`, `.blog`, `.store`, `.tech`, `.pizza` | Expanded namespace |

There are now over **1,500 TLDs**. `.com` remains by far the most common and trusted.

### Second-Level Domain (SLD)

The part you register — `google`, `github`, `example`. This is the name you pay for and own.

### Subdomain

Prefixes that the domain owner controls — `www`, `blog`, `mail`, `api`, `shop`. A subdomain is a separate DNS record pointing to the same or different server.

Examples:
- `www.example.com` — the main website
- `mail.example.com` — the email server
- `api.example.com` — the API server
- `docs.example.com` — documentation
- `status.example.com` — status page

The owner of `example.com` can create unlimited subdomains — no registration needed.

---

## ICANN: Who Controls Domain Names?

**ICANN** (Internet Corporation for Assigned Names and Numbers) is the nonprofit organization that coordinates the global DNS system:

- Manages the list of approved TLDs
- Accredits domain registrars (companies allowed to sell domain registrations)
- Maintains the root zone — the authoritative list of all TLD nameservers
- Delegates ccTLDs to country-level authorities

### Domain Registrars

To register a domain, you use an **accredited registrar** — companies like Namecheap, GoDaddy, Google Domains (now Squarespace), Cloudflare, or hundreds of others. You pay an annual fee (typically $10–$20/year for `.com`) to lease the domain name.

When you register `mysite.com`:
1. The registrar tells the `.com` TLD registry (VeriSign) that you own `mysite.com`
2. The TLD registry publishes your domain's **nameservers** — the servers that hold your DNS records
3. You configure your DNS records at those nameservers to point to your web server

---

## DNS Resolution: The Full Journey

We covered DNS resolution at a high level in Section 2. Here's a more detailed view with the web context:

```
You type: https://blog.example.com

Step 1: Browser DNS Cache
  └── Did I resolve this recently? → No

Step 2: OS DNS Cache
  └── Does the OS have a cached answer? → No

Step 3: OS checks /etc/hosts (macOS/Linux) or %WINDIR%\System32\drivers\etc\hosts (Windows)
  └── Is there a manual entry? → No
  └── (This file overrides DNS — used for development, parental controls, blocking ads)

Step 4: Query the Recursive Resolver
  └── Usually your ISP's DNS server, or a public one:
      • 8.8.8.8 (Google Public DNS)
      • 1.1.1.1 (Cloudflare — fastest globally)
      • 9.9.9.9 (Quad9 — privacy-focused)

Step 5: Resolver checks its own cache → Not found

Step 6: Resolver asks a Root Server
  └── "Who handles .com?"
  └── 13 root server clusters exist worldwide (a-m.root-servers.net)
  └── Root: "For .com, ask these TLD nameservers: a.gtld-servers.net..."

Step 7: Resolver asks the .com TLD Server
  └── "Who handles example.com?"
  └── TLD: "The nameservers for example.com are ns1.example.com and ns2.example.com"

Step 8: Resolver asks example.com's Authoritative Nameserver
  └── "What is the IP address of blog.example.com?"
  └── Authoritative NS: "blog.example.com = 93.184.216.34, TTL=3600"

Step 9: Resolver caches the answer (for 3600 seconds = 1 hour)
  └── Returns 93.184.216.34 to your OS

Step 10: OS caches the answer
  └── Returns to browser

Step 11: Browser caches the answer
  └── Makes TCP connection to 93.184.216.34:443
```

**Total time on first lookup:** 20–120ms
**Total time from cache:** < 1ms

---

## DNS Record Types

Each domain name has **DNS records** stored in its authoritative nameserver. Records tell the Internet what to do with requests for that domain.

### A Record — IPv4 Address

```
blog.example.com    A    93.184.216.34    TTL=3600
```

Maps a hostname to an IPv4 address. The most common record type.

### AAAA Record — IPv6 Address

```
blog.example.com    AAAA    2606:2800:220:1:248:1893:25c8:1946    TTL=3600
```

Maps a hostname to an IPv6 address. Modern sites have both A and AAAA records.

### CNAME — Canonical Name (Alias)

```
www.example.com     CNAME   example.com     TTL=3600
```

An alias — `www.example.com` points to whatever `example.com` points to. If you change `example.com`'s A record, `www` automatically follows.

> **Common use:** CDN integration. Instead of pointing `example.com` to a server IP directly, you create a CNAME to `example.com.cdn-provider.net` — the CDN handles routing to the fastest edge server.

### MX — Mail Exchange

```
example.com    MX    10    mail1.example.com    TTL=3600
example.com    MX    20    mail2.example.com    TTL=3600
```

Tells email servers where to deliver mail for `@example.com`. The number is priority — lower = higher priority. If `mail1` is down, the sender tries `mail2`.

### TXT — Text Record

```
example.com    TXT    "v=spf1 include:_spf.google.com ~all"
```

Free-form text. Used for:
- **SPF** — specify which servers are allowed to send email for the domain (fights spoofing)
- **DKIM** — email authentication public keys
- **Domain verification** — "put this TXT record to prove you own the domain" (Google, Stripe, etc.)

### NS — Nameserver

```
example.com    NS    ns1.example.com    TTL=172800
example.com    NS    ns2.example.com    TTL=172800
```

Specifies which servers hold the authoritative DNS records for the domain. Delegated by the TLD registry when you register.

### PTR — Pointer (Reverse DNS)

```
34.216.184.93.in-addr.arpa    PTR    blog.example.com
```

The reverse of an A record — maps an IP to a hostname. Used by email servers to verify that a sending server's IP matches its claimed hostname (helps fight spam).

---

## TTL: Why DNS Changes Are Slow

Every DNS record has a **TTL (Time to Live)** — the number of seconds resolvers and browsers should cache the answer before re-querying.

```
example.com    A    93.184.216.34    TTL=86400
                                         ↑
                                    24 hours
```

If you change your domain's A record (e.g., migrating to a new server), resolvers worldwide will keep serving the old answer until their cached copy expires. With TTL=86400, it could take **up to 24 hours** for all resolvers to pick up the change.

**Best practice before a migration:**
1. Lower your TTL to 300 (5 minutes) a few days before the change
2. Make the IP change
3. Wait 5–10 minutes for propagation
4. Restore your TTL to normal once the migration is stable

---

## CDNs and GeoDNS

### CDN (Content Delivery Network)

A **CDN** is a globally distributed network of servers (called **edge servers** or **PoPs — Points of Presence**) that cache copies of your content near users.

When you use a CDN like Cloudflare, Fastly, or AWS CloudFront:
1. A user in Tokyo requests your website
2. DNS resolves to the nearest CDN edge server (in Tokyo or Osaka)
3. If the edge has a cached copy — it serves it immediately (no trip to your origin server in, say, New York)
4. If not — the edge fetches from your origin, caches it, and serves it

**Result:** Users around the world get fast responses regardless of where your origin server is located.

### GeoDNS

**GeoDNS** (also called Anycast DNS) returns different IP addresses to different users based on their geographic location — directing them to the nearest CDN edge or server cluster.

```
User in New York  → dig example.com → 151.101.1.140  (Fastly NYC edge)
User in London    → dig example.com → 151.101.129.140 (Fastly London edge)
User in Singapore → dig example.com → 151.101.193.140 (Fastly Singapore edge)
```

Same domain name, different IP answers — based on where the query came from. This is how large websites serve global audiences with low latency.

**Further Reading:**
- [How DNS Works — Comic (howdns.works)](https://howdns.works/)
- [DNS Records Explained — Cloudflare](https://www.cloudflare.com/learning/dns/dns-records/)
- [What Is a CDN? — Cloudflare](https://www.cloudflare.com/learning/cdn/what-is-a-cdn/)
- [ICANN](https://www.icann.org/)

---

## Check Your Understanding

1. What are the three parts of `blog.example.com` and what does each represent?
2. Why do DNS changes sometimes take up to 48 hours to propagate globally?
3. What is the difference between an A record and a CNAME record?
4. Why would a company lower their TTL before migrating to a new server?
5. What does a CDN do, and how does GeoDNS help deliver content faster globally?

---

*Next: [02 — Clients and Servers](02-clients-and-servers.md)*
