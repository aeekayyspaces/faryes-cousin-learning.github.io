# 05 — Networking in Action: curl, telnet, and netcat

## Learning Objectives

By the end of this page, you will be able to:

- Use `curl` to make HTTP requests from the command line
- Inspect HTTP request and response headers
- Use `telnet` to connect to servers and send raw protocol commands
- Use `netcat` (`nc`) to create simple direct socket connections
- Understand what each tool reveals about the underlying protocols

---

## Prerequisites: Installing the Tools

### macOS

`curl` is pre-installed. For `telnet` and `netcat`:

```bash
# Install Homebrew if you haven't (from https://brew.sh):
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install telnet
brew install telnet

# netcat (nc) is pre-installed on macOS
nc --version
```

### Linux (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install curl telnet netcat-openbsd
```

### Windows

`curl` is built into Windows 10 and 11 (PowerShell and CMD).

For `telnet`: Open **Control Panel → Programs → Turn Windows features on or off** → check **Telnet Client** → OK.

For `nc` (netcat): Install via **WSL (Windows Subsystem for Linux)** or download [nmap's ncat](https://nmap.org/ncat/). The examples below assume WSL or a bash environment.

---

## Part 1: curl

`curl` (Client URL) is a command-line tool for making network requests. It supports HTTP, HTTPS, FTP, SMTP, and more. Every web developer uses it constantly.

> **Analogy:** curl is like a scriptable browser without a graphical interface. It sends requests and shows you exactly what the server returns — no rendering, no hiding, just raw data.

### Basic GET Request

```bash
curl https://httpbin.org/get
```

`httpbin.org` is a free testing service that echoes back information about your request. Output:

```json
{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Host": "httpbin.org",
    "User-Agent": "curl/7.88.1"
  },
  "origin": "203.0.113.50",
  "url": "https://httpbin.org/get"
}
```

Notice the server received your `User-Agent` (curl's identifier) and your public IP address.

---

### See Request and Response Headers: `-v`

The `-v` (verbose) flag shows everything — the full HTTP conversation:

```bash
curl -v https://httpbin.org/get
```

Output (abbreviated):
```
*   Trying 54.167.22.122:443...
* Connected to httpbin.org (54.167.22.122) port 443
* TLS 1.3 connection using TLS_AES_256_GCM_SHA384
* Server certificate: httpbin.org
> GET /get HTTP/2
> Host: httpbin.org
> user-agent: curl/7.88.1
> accept: */*
>
< HTTP/2 200
< content-type: application/json
< content-length: 256
<
{ ...response body... }
```

Lines starting with `>` are your **request**. Lines starting with `<` are the **response**. Lines starting with `*` are curl's own status messages.

---

### See Only the Response Headers: `-I`

```bash
curl -I https://google.com
```

Output:
```
HTTP/2 301
location: https://www.google.com/
content-type: text/html; charset=UTF-8
date: Tue, 06 May 2026 10:00:00 GMT
server: gws
```

`301` means Google is redirecting you from `google.com` to `www.google.com`.

---

### Follow Redirects: `-L`

```bash
curl -L https://google.com
```

The `-L` flag tells curl to follow redirects. Without it, you'd get the `301` response body (usually a short HTML page saying "moved"). With `-L`, curl follows to the final destination.

---

### POST Request with Data: `-X POST -d`

```bash
curl -X POST https://httpbin.org/post \
  -H "Content-Type: application/json" \
  -d '{"name": "Alex", "age": 25}'
```

- `-X POST` — use the POST method
- `-H "Content-Type: application/json"` — add a request header
- `-d '...'` — the request body (data to send)

Output will echo back the JSON you sent.

---

### Send a Form POST

```bash
curl -X POST https://httpbin.org/post \
  -d "username=alex&password=secret123"
```

This mimics a traditional HTML form submission.

---

### Save Response to a File: `-o`

```bash
curl -o homepage.html https://example.com
```

Saves the response body to `homepage.html` — essentially downloading the raw HTML of a web page.

---

### Download a File: `-O`

```bash
curl -O https://example.com/file.pdf
```

`-O` (capital O) saves the file using the server's filename.

---

### Set a Custom User-Agent

```bash
curl -A "MyLearningBot/1.0" https://httpbin.org/get
```

Servers use the User-Agent header to identify clients. Some servers respond differently based on whether you're a browser or a bot. This shows you can set it to anything.

---

### Send a Cookie

```bash
curl -b "session=abc123; user=alex" https://httpbin.org/cookies
```

---

### Add an Authorization Header

```bash
curl -H "Authorization: Bearer mytoken123" https://httpbin.org/bearer
```

---

### Check an HTTPS Certificate

```bash
curl -v https://expired.badssl.com/ 2>&1 | grep "certificate\|SSL\|TLS\|error"
```

`badssl.com` has intentionally broken TLS configurations for testing. `expired.badssl.com` has an expired certificate — curl will refuse to connect and show an error. This is the TLS system protecting you.

To bypass (for testing only — **never in production**):
```bash
curl -k https://expired.badssl.com/    # -k = ignore certificate errors
```

---

### Measure Response Time

```bash
curl -o /dev/null -s -w "DNS: %{time_namelookup}s\nConnect: %{time_connect}s\nTLS: %{time_appconnect}s\nTotal: %{time_total}s\n" https://google.com
```

This measures how long each phase took:
- **DNS lookup** — resolving the hostname
- **TCP connect** — establishing the connection
- **TLS handshake** — setting up encryption
- **Total** — everything combined

---

## Part 2: telnet

`telnet` establishes a raw TCP connection and lets you type protocol commands by hand. While mostly obsolete for actual remote access (replaced by SSH), it's an excellent learning tool — it shows you the raw protocol conversation that tools like curl do automatically.

> **Analogy:** If curl is driving a car with automatic transmission, telnet is driving one with manual transmission. You have to do everything yourself, but you understand every gear change.

### Connect to a Web Server Directly

This shows you exactly what an HTTP request looks like at the protocol level:

```bash
telnet example.com 80
```

After connecting, you'll see:
```
Trying 93.184.216.34...
Connected to example.com.
Escape character is '^]'.
```

Now type the HTTP request **exactly** — including headers and the blank line that ends the headers. Press Enter twice after the last header:

```
GET / HTTP/1.1
Host: example.com

```

The server will respond with raw HTTP — headers followed by the HTML body. You're reading an actual HTTP response with your own eyes.

```
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Length: 1256
...

<!doctype html>
<html>
<head>
    <title>Example Domain</title>
...
```

Press `Ctrl + ]` then type `quit` to exit telnet.

> **Note:** This only works on port 80 (plain HTTP). Port 443 (HTTPS) uses TLS encryption, so you'd see garbled output — you can't manually type an encrypted connection. HTTPS is why modern web browsing can't be eavesdropped — even if you intercept the bytes, they're encrypted.

---

### Connect to Other Services

Telnet can connect to any TCP port. Here are a few interesting ones:

**Check if a port is open:**
```bash
telnet google.com 443
```
If it connects (shows "Connected"), port 443 is open. Press `Ctrl + ]`, then `quit`.

**Connect to a local service:**
```bash
# If you have a local web server running on port 3000:
telnet localhost 3000
```

---

### Testing SMTP (Email) Manually

This is advanced, but shows how email works at the protocol level. Most servers require authentication today, but you can observe the handshake:

```bash
telnet smtp.gmail.com 587
```

You'll see:
```
220 smtp.gmail.com ESMTP
```

SMTP uses simple text commands — `EHLO`, `MAIL FROM:`, `RCPT TO:`, `DATA`, `QUIT`. Every email client follows these commands under the hood.

---

## Part 3: netcat (nc)

**Netcat** is often called the "Swiss Army knife" of networking. It can create raw TCP or UDP connections in both directions — listen for connections, connect to ports, transfer data, and more.

### Simple Connection Test

Like telnet, but lighter:
```bash
nc -v google.com 80
```

Then type an HTTP request manually (same as the telnet example above).

---

### Listen on a Port

```bash
nc -l 9999
```

This opens port 9999 and waits for a connection. Nothing happens until someone connects.

---

### The P2P Chat Demo

This demonstrates direct peer-to-peer communication — two terminals talking directly to each other with no server in between.

**Open two terminal windows** (or two tabs).

**Terminal 1 — the listener:**
```bash
nc -l 9999
```

**Terminal 2 — the connector:**
```bash
nc localhost 9999
```

Now type in either terminal and press Enter. The text appears in the other terminal. You've created a direct, two-way communication channel — no web server, no cloud service, just raw TCP.

This is the fundamental building block of all networking: one process opens a port, another connects, and they exchange data directly.

Press `Ctrl + C` in either terminal to close the connection.

---

### Transfer a File with netcat

You can use netcat to send files directly between two machines on the same network.

**On the receiving machine (Terminal 1):**
```bash
nc -l 9999 > received_file.txt
```

**On the sending machine (Terminal 2):**
```bash
nc [receiver-IP] 9999 < file_to_send.txt
```

The file transfers directly — no FTP server, no cloud storage, no HTTP. This is raw TCP file transfer.

> **Note:** This is unencrypted. For real file transfers between computers, use `scp` (SSH copy) or SFTP instead.

---

### Scan a Port

```bash
nc -zv google.com 80
nc -zv google.com 443
nc -zv localhost 3000
```

`-z` = don't send data (just check if port is open), `-v` = verbose (print result).

Output: `Connection to google.com 443 port [tcp/https] succeeded!`

This is a simple way to check if a service is running on a given port.

---

### Scan a Port Range

```bash
nc -zv localhost 8000-8100
```

Checks all ports from 8000 to 8100 on localhost. Useful for finding what services are running locally.

---

## Putting It Together: What These Tools Reveal

These three tools collectively show you the layers of the network stack in action:

| Tool | What It Reveals |
|------|----------------|
| `curl` | The Application Layer — HTTP requests, headers, status codes, TLS certificates |
| `telnet` | The Transport Layer made visible — raw TCP connection, manual protocol commands |
| `nc` | The Network primitives — listening sockets, raw data streams, direct connections |

When you use a web browser, it's doing everything `curl` does (and more) for every page, image, script, and stylesheet. The browser just hides it behind a graphical interface.

---

## Exercises

Work through these exercises on your machine. Answer keys in [solutions/05-networking-solutions.md](solutions/05-networking-solutions.md).

### Exercise 1: curl Basics
1. Make a GET request to `https://httpbin.org/get` and observe the response.
2. Make the same request with `-v` and identify the request headers your machine sent.
3. Make a GET request to `https://httpbin.org/headers` — what does it show?
4. Use `-I` to get only the headers from `https://github.com`. What is the HTTP status code?

### Exercise 2: curl POST
1. Send a POST request to `https://httpbin.org/post` with a JSON body containing your name and a favorite color.
2. Observe the response — does the server echo your JSON back?
3. Send the same POST using form encoding (`-d "name=Alex&color=blue"` without the JSON Content-Type header). How does the response differ?

### Exercise 3: curl Inspection
1. Download the HTML of `https://example.com` and save it to `example.html`.
2. Open `example.html` in a text editor. Can you identify the `<title>` tag?
3. Use curl to measure the response time of three different websites. Which is fastest from your location?

### Exercise 4: telnet HTTP
1. Use telnet to connect to `example.com` on port 80.
2. Type a manual `GET / HTTP/1.1` request with a `Host:` header.
3. Copy the HTTP status code and one header from the response.

### Exercise 5: netcat Chat
1. Open two terminal windows.
2. Start a listener on port 8888 in Terminal 1.
3. Connect to it from Terminal 2.
4. Send three messages back and forth.
5. Close the connection.

### Exercise 6: Port Scanning
1. Check if port 80 is open on `google.com`.
2. Check if port 22 (SSH) is open on `google.com`.
3. Check if port 3000 is open on `localhost` (it probably isn't — that's fine, note what message you get).

---

## Check Your Understanding

1. What does `curl -v` reveal that a normal browser doesn't show you?
2. Why can't you use telnet to manually make an HTTPS request (port 443)?
3. What does the `-L` flag do in curl? When would you need it?
4. In the netcat P2P demo, which machine is the "server" and which is the "client"? Does that distinction really matter?
5. What does a `curl -I` response with a `301` status tell you about a website?

---

*Previous: [04 — P2P Networks](04-p2p-networks.md)*
*Next: [06 — Security and Encryption](06-security-and-encryption.md)*
