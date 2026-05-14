# Solutions — 05 Networking in Action Exercises and Code Challenges

Answer key for exercises in [05 — Networking in Action](../05-networking-in-action.md) and code challenges from [07 — Quiz and Checkpoints](../07-quiz-and-checkpoints.md).

---

## Exercise 1: curl Basics

### 1. GET request to httpbin

```bash
curl https://httpbin.org/get
```

Expected response structure:
```json
{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Host": "httpbin.org",
    "User-Agent": "curl/7.xx.x",
    "X-Amzn-Trace-Id": "Root=..."
  },
  "origin": "203.0.113.50",   ← your public IP
  "url": "https://httpbin.org/get"
}
```

### 2. Verbose mode

```bash
curl -v https://httpbin.org/get
```

In the output, lines beginning with `>` are your request headers. You'll see headers like:
```
> GET /get HTTP/2
> Host: httpbin.org
> user-agent: curl/7.88.1
> accept: */*
```

And lines beginning with `<` are response headers:
```
< HTTP/2 200
< content-type: application/json
< content-length: 290
```

### 3. /headers endpoint

```bash
curl https://httpbin.org/headers
```

This returns only the headers your request sent. Compare with `/get` — `/headers` shows fewer fields (no args, origin, url — just the headers dictionary).

### 4. GitHub headers only

```bash
curl -I https://github.com
```

You'll typically see a redirect:
```
HTTP/2 200
server: GitHub.com
content-type: text/html; charset=utf-8
...
```

Note: GitHub sometimes returns `200` directly rather than redirecting, depending on your request headers.

---

## Exercise 2: curl POST

### 1. JSON POST

```bash
curl -X POST https://httpbin.org/post \
  -H "Content-Type: application/json" \
  -d '{"name": "Alex", "color": "blue"}'
```

Response snippet:
```json
{
  "data": "{\"name\": \"Alex\", \"color\": \"blue\"}",
  "json": {
    "color": "blue",
    "name": "Alex"
  },
  ...
}
```

Notice two fields: `data` (raw string) and `json` (parsed object) — because you specified `Content-Type: application/json`.

### 2. Form encoding

```bash
curl -X POST https://httpbin.org/post \
  -d "name=Alex&color=blue"
```

Response snippet:
```json
{
  "form": {
    "color": "blue",
    "name": "Alex"
  },
  "data": "",
  "json": null,
  ...
}
```

**Key difference:** With JSON content type, the body appears in `data` and `json`. With form encoding (no JSON content type), the body is parsed into `form`. The server interprets the data differently based on `Content-Type`.

---

## Exercise 3: curl Inspection

### 1. Download HTML

```bash
curl -o example.html https://example.com
```

### 2. Find the title

Open `example.html` in any text editor. Look for:
```html
<title>Example Domain</title>
```

### 3. Response time comparison

```bash
curl -o /dev/null -s -w "Total: %{time_total}s\n" https://google.com
curl -o /dev/null -s -w "Total: %{time_total}s\n" https://github.com
curl -o /dev/null -s -w "Total: %{time_total}s\n" https://amazon.com
```

Results vary by location. Typically, `google.com` will be fastest for most users due to Google's extensive global CDN infrastructure placing servers close to users worldwide.

**What to notice:** Response times reflect your distance (in network hops) to the server, the server's load, and whether content is cached at a CDN edge near you.

---

## Exercise 4: telnet HTTP

### Commands

```bash
telnet example.com 80
```

After connection:
```
GET / HTTP/1.1
Host: example.com

```
(Type the blank line by pressing Enter twice)

### Expected Response

```
HTTP/1.1 200 OK
Content-Encoding: gzip
Accept-Ranges: bytes
Age: 12345
Cache-Control: max-age=604800
Content-Type: text/html; charset=UTF-8
...
```

**What to record:**
- Status code: `200`
- One header: e.g., `Content-Type: text/html; charset=UTF-8`

**Why this matters:** You just manually reproduced exactly what a web browser does for every single page load — except the browser does it invisibly and millions of times faster.

---

## Exercise 5: netcat Chat

### Commands

**Terminal 1:**
```bash
nc -l 9999
```

**Terminal 2:**
```bash
nc localhost 9999
```

**What happens:** Once Terminal 2 connects, both terminals share a single TCP stream. Anything typed in either terminal (followed by Enter) appears in the other.

**What to notice:**
- There is no "server" in the traditional sense — both sides have equal capability
- The connection is a raw TCP stream — no HTTP, no protocol, just bytes
- When either side presses `Ctrl+C`, the connection closes and both terminals exit

---

## Code Challenge Solutions

### Challenge 1: curl Exploration

```bash
# 1. Verbose request, save to file
curl -v https://httpbin.org/get > curl-investigation.txt 2>&1

# 2. Find details by reading curl-investigation.txt
# Or extract specific values:
curl -s https://httpbin.org/get | grep -E '"origin"|"User-Agent"|"url"'
```

**Adding a custom header:**
```bash
curl -v -H "X-My-Name: Alex" https://httpbin.org/get
```

In the response `headers` object, you'll see your custom header echoed back:
```json
"headers": {
  "X-My-Name": "Alex",
  ...
}
```

**What to record:**
- HTTP version: usually `HTTP/2` for modern sites
- Your public IP: shown in `"origin"`
- User-Agent: `curl/7.x.x`
- Status code: `200`

---

### Challenge 2: HTTP Methods

**GET:**
```bash
curl -s https://httpbin.org/anything
```

**POST with JSON:**
```bash
curl -s -X POST https://httpbin.org/anything \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello from curl", "section": 2}'
```

**Key differences between GET and POST responses:**
- GET response: `"method": "GET"`, empty `data`, empty `json`
- POST response: `"method": "POST"`, `data` contains your JSON string, `json` contains the parsed object

**DELETE:**
```bash
curl -s -X DELETE https://httpbin.org/anything
```

httpbin responds with `200 OK` for DELETE — it mirrors back whatever method you used. In a real API, DELETE would typically return `204 No Content` (success, no body) or `200 OK` with confirmation.

---

### Challenge 3: Certificate Inspector

**Connect and capture output:**
```bash
echo | openssl s_client -connect github.com:443 2>/dev/null | openssl x509 -noout -text
```

**Find Subject Alternative Names (SANs):**
Look for the `Subject Alternative Names` section in the output:
```
X509v3 Subject Alternative Names:
    DNS:github.com, DNS:www.github.com
```

**Find the issuer:**
```
Issuer: C = US, O = DigiCert Inc, CN = DigiCert TLS Hybrid ECC SHA384 2020 CA1
```

**Find the expiry date:**
```bash
echo | openssl s_client -connect github.com:443 2>/dev/null | openssl x509 -noout -dates
```

```
notBefore=Mar  7 00:00:00 2026 GMT
notAfter=Mar  8 23:59:59 2027 GMT
```

**Expired certificate test:**
```bash
openssl s_client -connect expired.badssl.com:443
```

You'll see an error like:
```
verify error:num=10:certificate has expired
verify return:0
```

**curl test:**
```bash
curl https://expired.badssl.com/
```
Output: `curl: (60) SSL certificate problem: certificate has expired`

curl refuses to connect because it detected the expired certificate — this is the TLS system doing its job and protecting you.

---

### Challenge 4: The P2P Chat

**Terminal 1:**
```bash
nc -l 7777
```

**Terminal 2:**
```bash
nc localhost 7777
```

**What to observe:**
- After `nc localhost 7777`, both terminals are connected via a direct TCP stream
- Messages typed in either terminal appear in the other after pressing Enter
- No server is involved — this is a direct socket connection
- The connection is TCP — reliable, ordered, no data loss

**Bonus — File Transfer:**

Terminal 1 (receiver):
```bash
nc -l 7777 > received.txt
```

Terminal 2 (sender):
```bash
cat somefile.txt | nc localhost 7777
# or:
nc localhost 7777 < somefile.txt
```

When the file finishes sending, the sender can close the connection with `Ctrl+C`. The receiver will exit and the file will be written.

**What this demonstrates:** BitTorrent, at its core, does something similar — just with many simultaneous connections, piece tracking, and hash verification on top.

---

### Challenge 5: DNS Investigation

**macOS/Linux:**
```bash
# 1. IP addresses for github.com
dig github.com A
# Typical result: github.com → 140.82.113.4 (or similar; may vary)

# 2. Mail servers for google.com
dig google.com MX
# Results: gmail-smtp-in.l.google.com (priority 5, 10, etc.)

# 3. Nameservers for cloudflare.com
dig cloudflare.com NS
# Results: ns1.cloudflare.com, ns2.cloudflare.com, etc.

# 4. Multiple IPs for google.com
dig google.com A
```

**Windows:**
```powershell
Resolve-DnsName github.com
Resolve-DnsName google.com -Type MX
Resolve-DnsName cloudflare.com -Type NS
```

**Why does google.com have multiple IPs?**

Google returns different IP addresses to different users based on their location (GeoDNS). Each IP points to a different Google data center — the one closest to the user. This is called **Anycast routing** and **load balancing**:
- Reduces latency (closer server = faster response)
- Distributes load across many servers
- Provides redundancy (if one data center fails, others serve the traffic)
- Enables DDoS resistance (attack traffic gets spread across many locations)

When you `dig google.com` multiple times, you may get different answers each time as Google rotates IPs. This is normal and intentional.

---

*Back to exercises: [05 — Networking in Action](../05-networking-in-action.md)*
*Back to quiz: [07 — Quiz and Checkpoints](../07-quiz-and-checkpoints.md)*
