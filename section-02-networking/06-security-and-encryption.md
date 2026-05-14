# 06 — Security and Encryption: SSL/TLS and openssl

## Learning Objectives

By the end of this page, you will be able to:

- Explain why encryption is essential for modern networking
- Distinguish between symmetric and asymmetric encryption
- Describe how the TLS handshake establishes an encrypted connection
- Understand what digital certificates are and what they prove
- Use `openssl` to inspect certificates, test connections, and encrypt files
- Recognize common certificate errors and what they mean

---

## Why Encryption Matters

When you connect to a website without encryption (plain HTTP), your data travels in cleartext through dozens of routers and networks before reaching the server. Any of them — an ISP, a router at a coffee shop, an attacker on the same Wi-Fi — can:

- **Read** your data (passwords, messages, credit card numbers)
- **Modify** your data in transit (swap content, inject ads or malware)
- **Impersonate** the server and steal your credentials

> **Analogy:** Sending data without encryption is like sending a postcard — anyone who handles it along the way can read it. Encryption turns it into a sealed envelope that only the intended recipient can open.

### Real Threats

- **Public Wi-Fi attacks:** An attacker on the same coffee shop Wi-Fi can intercept all unencrypted traffic with freely available tools
- **Man-in-the-Middle (MitM):** Attacker positions between client and server, reading and potentially altering all communications
- **ISP monitoring:** Your ISP can read and log all unencrypted traffic — and in many countries, is required to do so
- **Passive surveillance:** State-level actors monitor Internet backbone traffic

**This is why HTTPS is now considered the baseline for all web traffic, not a luxury.**

---

## Symmetric Encryption

**Symmetric encryption** uses the same key to both encrypt and decrypt data.

```
Plaintext + Key → [Encryption] → Ciphertext
Ciphertext + Same Key → [Decryption] → Plaintext
```

> **Analogy:** A combination lock. If you and a friend both know the combination (the key), either of you can lock or unlock the safe. Simple — but you need a secure way to share the combination first.

**Common symmetric algorithms:**
- **AES (Advanced Encryption Standard)** — the current gold standard; 128-bit and 256-bit variants; used in TLS, file encryption, WPA2 Wi-Fi
- **ChaCha20** — fast on mobile hardware; used in TLS for mobile connections
- **3DES** — older; being phased out

**Strengths:** Very fast; efficient for large amounts of data.

**Weakness:** The **key exchange problem** — how do you securely share the secret key with someone before you have a secure channel? If you're communicating over an insecure network, an attacker could intercept the key and read everything.

---

## Asymmetric Encryption

**Asymmetric encryption** (also called **public-key cryptography**) uses a **pair** of mathematically related keys:

- **Public key** — you share this with everyone; anyone can use it to encrypt data
- **Private key** — you keep this secret; only you can use it to decrypt data

```
Plaintext + Public Key  → [Encryption] → Ciphertext
Ciphertext + Private Key → [Decryption] → Plaintext
```

> **Analogy:** Imagine a special padlock with a unique property. You give out hundreds of these padlocks (unlocked) to anyone who wants to send you a message. They put their message in a box, lock it with your padlock, and send it. Only you have the key to open the padlock. You freely distribute padlocks, but only you can unlock them. The padlock = public key; your key = private key.

**Common asymmetric algorithms:**
- **RSA** — widely used; key sizes of 2048 or 4096 bits
- **ECDSA / ECDH** (Elliptic Curve) — smaller keys, same security level, faster; used in modern TLS
- **Ed25519** — fast and secure; used in SSH keys

**Strengths:** Solves the key exchange problem — you can publish your public key openly.

**Weakness:** Much slower than symmetric encryption; not practical for encrypting large data.

**How both are used together in TLS:** Asymmetric encryption secures the initial key exchange; then both parties switch to fast symmetric encryption for the actual data transfer.

---

## Digital Signatures

Asymmetric keys can also be used in **reverse** for signing:

- Encrypt with **private key** → only you could have produced this → proves authorship
- Decrypt with **public key** → anyone can verify the signature

This proves a message came from the holder of the private key and wasn't altered in transit.

```
Message + Private Key → [Sign] → Signature
Message + Signature + Public Key → [Verify] → "Valid" or "Invalid"
```

**Used for:** Code signing (verifying software hasn't been tampered with), email signing (S/MIME), TLS certificates, SSH authentication, Git commit signing.

---

## TLS: How HTTPS Actually Works

**TLS (Transport Layer Security)** is the protocol that secures HTTPS connections. It was formerly called **SSL (Secure Sockets Layer)** — SSL 3.0 was replaced by TLS 1.0 in 1999, and TLS is the current standard (TLS 1.2 and TLS 1.3). The term "SSL" is still used casually to refer to TLS.

### The TLS Handshake (Simplified)

Before any encrypted data flows, client and server perform a **handshake** to:
1. Agree on which encryption algorithms to use
2. Exchange or generate a shared session key
3. Verify the server's identity

**TLS 1.3 Handshake (simplified):**

```
Client                                    Server
  │                                          │
  │──── ClientHello ────────────────────────▶│
  │     (TLS version, cipher suites,         │
  │      random number, key share)           │
  │                                          │
  │◀─── ServerHello ────────────────────────│
  │     (chosen cipher, random number,       │
  │      key share, Certificate)             │
  │                                          │
  │  [Both sides derive session keys         │
  │   from shared key material]              │
  │                                          │
  │──── Finished (encrypted) ───────────────▶│
  │     (verifies handshake integrity)       │
  │                                          │
  │◀─── Finished (encrypted) ───────────────│
  │                                          │
  │══════ Encrypted Application Data ════════│
  │  (HTTP requests and responses)           │
```

After the handshake, all data is encrypted with the agreed session keys. TLS 1.3 reduced the handshake from 2 round trips (TLS 1.2) to 1 round trip — making HTTPS faster.

---

## Digital Certificates

During the TLS handshake, the server sends a **certificate**. The certificate answers: **"How do I know this server is really who it claims to be?"**

> **Analogy:** A TLS certificate is like a passport. When you enter a country, the border guard checks your passport — not just that it exists, but that a trusted government issued and signed it. If you handed over a hand-drawn fake "passport," it would be rejected. TLS certificates work the same way: your browser only trusts certificates signed by recognized authorities.

### What's in a Certificate?

A certificate contains:
- **Domain name(s)** it's valid for (`example.com`, `*.example.com`)
- **Public key** of the server
- **Issuer** — which Certificate Authority signed it
- **Validity period** — not before / not after dates
- **Digital signature** — the CA's cryptographic signature proving it's genuine

### Certificate Authorities (CAs)

A **Certificate Authority (CA)** is a trusted organization that issues certificates after verifying the requester actually controls the domain.

**Major CAs:**
- DigiCert, Comodo/Sectigo, GlobalSign, GoDaddy (commercial)
- **Let's Encrypt** — free, automated, open CA; now the most-used CA on the Internet
- Government CAs (various countries)

Your operating system and browser come pre-loaded with a list of trusted CAs (the "root store"). A certificate is trusted only if it chains back to one of these roots.

### Certificate Chain of Trust

```
Root CA Certificate (trusted by browsers/OS)
    └── Intermediate CA Certificate
            └── Your Site's Certificate
                   (signed by Intermediate CA)
```

Browsers rarely trust site certificates directly from a root CA. Intermediate CAs add a layer — if an intermediate is compromised, only certificates signed by that intermediate need to be revoked, not the root.

### Types of Certificates

| Type | Validation | What's Verified | Trust Level |
|------|-----------|----------------|-------------|
| **DV (Domain Validation)** | Automated | Domain ownership only | Basic |
| **OV (Organization Validation)** | Manual | Domain + organization identity | Medium |
| **EV (Extended Validation)** | Rigorous | Legal entity + domain | High |

Most websites use DV certificates (including Let's Encrypt). EV certificates show the organization name in some browsers.

---

## Common Certificate Errors

| Error | Meaning |
|-------|---------|
| `NET::ERR_CERT_EXPIRED` | Certificate's validity period has passed — the site owner forgot to renew |
| `NET::ERR_CERT_AUTHORITY_INVALID` | Certificate not signed by a trusted CA — possibly self-signed, or a MitM attack |
| `NET::ERR_CERT_COMMON_NAME_INVALID` | Certificate is for a different domain — e.g., cert says `example.com` but you're on `other.com` |
| `NET::ERR_CERT_REVOKED` | Certificate was revoked (possibly due to a private key compromise) |

When you see a certificate error, your browser is protecting you from a potentially dangerous situation. These errors shouldn't be bypassed casually.

---

## openssl: Hands-On Encryption

`openssl` is a command-line toolkit for TLS, certificates, and cryptographic operations. It's installed by default on macOS and most Linux systems.

**Windows:** Available in Git Bash, WSL, or by installing OpenSSL separately.

### Inspect a Website's Certificate

```bash
openssl s_client -connect google.com:443 -showcerts
```

This opens a TLS connection and shows the full certificate chain. Press `Ctrl+C` to exit.

Look for:
```
Certificate chain
 0 s:CN = *.google.com         ← your site's cert
   i:C = US, O = Google Trust Services, CN = WR2  ← issued by
 1 s:C = US, O = Google Trust Services, CN = WR2  ← intermediate CA
   i:C = US, O = Google Trust Services, CN = GTS Root R1  ← root CA
```

### Show Certificate Details

```bash
echo | openssl s_client -connect github.com:443 2>/dev/null | openssl x509 -noout -text
```

This shows all the fields in the certificate including:
- Subject (who it's for)
- Issuer (who signed it)
- Validity dates
- Subject Alternative Names (all domains covered by the cert)
- Public key details
- Signature algorithm

### Check Certificate Expiry Date

```bash
echo | openssl s_client -connect example.com:443 2>/dev/null | openssl x509 -noout -dates
```

Output:
```
notBefore=Jan 15 00:00:00 2026 GMT
notAfter=Apr 15 23:59:59 2026 GMT
```

This is useful for monitoring whether a certificate is about to expire.

---

### Generate a Self-Signed Certificate

In development, you often need HTTPS locally without paying for a real certificate. A **self-signed certificate** is signed by you (not a trusted CA), so browsers will warn about it — but it still provides encryption.

```bash
# Generate a private key and self-signed certificate in one command
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes

# It will ask you for details:
# Country, State, City, Organization, Common Name (use "localhost" for local dev)
```

This creates two files:
- `key.pem` — your private key (keep this secret!)
- `cert.pem` — your certificate (share this with clients)

---

### Encrypt a File

Symmetric file encryption with AES-256:

```bash
# Encrypt a file (you'll be prompted for a password)
openssl enc -aes-256-cbc -salt -in secret.txt -out secret.txt.enc

# Decrypt it
openssl enc -d -aes-256-cbc -in secret.txt.enc -out decrypted.txt
```

---

### Generate a Random Secret Key

Useful for generating API keys, session secrets, or encryption keys:

```bash
openssl rand -hex 32        # 32 random bytes as hexadecimal
openssl rand -base64 32     # 32 random bytes as base64
```

---

### Hash a File

```bash
# SHA-256 hash of a file
openssl dgst -sha256 somefile.txt

# Or using sha256sum on Linux/macOS:
sha256sum somefile.txt
```

Hashes verify file integrity — if even one bit of the file changes, the hash changes completely. This is how download verification works (e.g., "verify your download matches this SHA256 checksum").

---

### Test TLS Version Support

```bash
# Test TLS 1.2
openssl s_client -connect example.com:443 -tls1_2

# Test TLS 1.3
openssl s_client -connect example.com:443 -tls1_3
```

If you see "Handshake failure," that TLS version isn't supported by the server.

---

## HTTPS in Practice: What Protects You

When you visit `https://yourbank.com`:

```
1. Browser looks up the IP address (DNS)

2. TCP connection established (3-way handshake)

3. TLS handshake:
   a. Server presents certificate for "yourbank.com"
   b. Browser verifies certificate is signed by a trusted CA
   c. Browser verifies cert is for "yourbank.com" (not "evilbank.com")
   d. Browser verifies cert hasn't expired
   e. Both sides agree on cipher suite and derive session keys

4. HTTP request sent — fully encrypted
   ← An attacker on the same Wi-Fi sees: [ENCRYPTED GIBBERISH]

5. Server responds — also encrypted
   ← An attacker sees: [MORE ENCRYPTED GIBBERISH]

6. Connection closed (or kept alive for more requests)
```

What TLS does NOT protect against:
- Attacks on the server itself
- Bugs in your browser or OS
- Malware already on your device
- Social engineering (phishing — a fake site can have valid HTTPS too)

> **Key Point:** HTTPS means "this connection is encrypted and I verified the server's identity." It does NOT mean "this website is trustworthy or safe." A phishing site can have a valid HTTPS certificate.

---

## A Note on HSTS

**HSTS (HTTP Strict Transport Security)** is a security header that tells browsers: "Always use HTTPS for this domain — never HTTP, even if the user types http://."

```
Strict-Transport-Security: max-age=31536000; includeSubDomains
```

This prevents **SSL stripping attacks**, where an attacker downgrades your HTTPS connection to HTTP before you notice.

---

## Check Your Understanding

1. What is the difference between symmetric and asymmetric encryption? When is each used in TLS?
2. What does a digital certificate prove? Who verifies that proof?
3. You see `NET::ERR_CERT_AUTHORITY_INVALID` when visiting a website. What could this mean?
4. Why doesn't a valid HTTPS certificate guarantee a website is safe to use?
5. What does `openssl rand -hex 32` produce, and why would you use it?

---

*Previous: [05 — Networking in Action](05-networking-in-action.md)*
*Next: [07 — Quiz and Checkpoints](07-quiz-and-checkpoints.md)*
