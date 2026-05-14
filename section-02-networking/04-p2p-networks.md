# 04 — P2P and Decentralized Networks

## Learning Objectives

By the end of this page, you will be able to:

- Explain the difference between client-server and peer-to-peer architectures
- Describe how BitTorrent distributes files without a central server
- Understand what a DHT (Distributed Hash Table) is at a high level
- Identify real-world applications built on P2P principles
- Weigh the trade-offs between centralized and decentralized systems

---

## Client-Server vs. Peer-to-Peer

### The Client-Server Model

In the **client-server model**, there is a clear hierarchy:

- **Servers** hold resources and respond to requests
- **Clients** request and consume those resources
- Communication flows: client → server → client

```
         [Server]
        /    |    \
  [Client] [Client] [Client]
```

**Examples:** Websites, email, online banking, streaming services

**Advantages:**
- Centralized control — easy to update, manage, and secure
- Consistent experience — server enforces rules
- Easy to monitor and audit

**Disadvantages:**
- **Single point of failure** — if the server goes down, nobody can connect
- **Scalability costs** — more users means more server hardware
- **Central chokepoint** — all bandwidth passes through the server
- **Censorship/control** — one entity controls access

### The Peer-to-Peer (P2P) Model

In a **peer-to-peer (P2P)** network, every participant is both a client *and* a server simultaneously. Peers connect directly to each other.

```
[Peer A] — [Peer B]
   |   \  /   |
[Peer D] — [Peer C]
```

**Examples:** BitTorrent, early Napster/Kazaa, Bitcoin/blockchain, some VoIP systems, Skype (historically), game update distribution (Steam)

**Advantages:**
- **No single point of failure** — the network survives individual peers leaving
- **Scales with participation** — more peers = more bandwidth available
- **Resilient to censorship** — no single server to shut down
- **Cheaper to operate** — no central server costs

**Disadvantages:**
- **Harder to control** — difficult to enforce rules or remove content
- **Security challenges** — you're connecting directly to unknown peers
- **Inconsistent performance** — depends on which peers are available
- **Complex to build** — decentralized coordination is hard to engineer

---

## File Sharing: Napster, Gnutella, BitTorrent

### Napster (1999–2001): P2P with a Central Directory

Napster was the first mainstream P2P file-sharing network. It was revolutionary — and illustrates why "pure" P2P is hard.

**How Napster worked:**
- A **central index server** tracked which users had which files
- Peers searched the central index for files
- Actual file transfers happened directly between peers
- This was a **hybrid** model: centralized discovery, decentralized transfer

**The flaw:** Because Napster had a central index server, the recording industry could (and did) sue Napster, and shut it down by taking out that single server. Napster ceased operations in 2001.

> **Lesson:** P2P file transfer without P2P discovery is not truly decentralized. A central server is still a single point of failure — and a single legal target.

### Gnutella (2000): True P2P

Gnutella had no central server at all. Peers flooded queries to their neighbors, who flooded to their neighbors.

**The flaw:** Flooding was massively inefficient. Searching generated enormous network traffic, and it scaled poorly as the network grew.

### BitTorrent (2001): Elegant P2P File Distribution

**BitTorrent**, created by Bram Cohen in 2001, solved the efficiency problem elegantly. It remains the most widely used P2P protocol today.

#### The Key Insight: Share While Downloading

In traditional downloads, you download a file from one server. You're a pure consumer. Only after you finish do you stop.

In BitTorrent, **you share pieces of the file as you download other pieces**. Every downloader is simultaneously an uploader. The more people downloading, the more people sharing — which makes downloads *faster* as a torrent becomes more popular.

#### How BitTorrent Works

**1. The .torrent file (or magnet link)**

A `.torrent` file is a small metadata file containing:
- A list of all the pieces the file is divided into
- A cryptographic **hash** of each piece (to verify integrity)
- The address of a **tracker** (a server that helps peers find each other)

**2. Tracker**

The tracker is a server that maintains a list of peers currently downloading or seeding a torrent. When you open a torrent, your client contacts the tracker: "I want this file — who else has it?"

The tracker returns a list of peers. Your client connects to many of them simultaneously.

> Note: Modern torrents increasingly use **trackerless** discovery via DHT (see below), reducing dependence on any single tracker.

**3. Pieces and Blocks**

The file is split into **pieces** (typically 256 KB – 1 MB each). Each piece is split into **blocks** (16 KB each) for transfer.

Your client downloads different pieces from different peers simultaneously — whichever peers have pieces you need and can serve them quickly.

**4. Rarest First**

BitTorrent clients prioritize downloading the **rarest pieces** first — pieces that fewest peers have. This ensures all pieces stay available in the swarm and prevents bottlenecks where everyone has the common pieces but nobody has the rare ones.

**5. Tit-for-Tat**

Peers prefer to upload to peers who are also uploading to them. This discourages **leeching** (downloading without uploading) and incentivizes contribution. Peers that don't share get slow download speeds.

**6. Seeds and Leechers**

- **Seeder:** A peer who has the complete file and is uploading to others
- **Leecher:** A peer who is downloading (and uploading what they have so far)
- More seeders = faster, more reliable downloads

```
       [Seeder: 100%]
      /       |       \
 [Leecher   [Leecher   [Leecher
  45%]        72%]       30%]
      \       |       /
       [Leecher: 10%]

Each leecher downloads different pieces from different sources simultaneously.
Each leecher also uploads to others what it has already downloaded.
```

**Further Reading:**
- [How BitTorrent Works — HowStuffWorks](https://computer.howstuffworks.com/bittorrent.htm)
- [BitTorrent Protocol Specification](https://www.bittorrent.org/beps/bep_0003.html)

---

## DHT — Distributed Hash Table

**DHT (Distributed Hash Table)** solves the tracker problem — how do peers find each other without any central server?

> **Analogy:** Imagine a phone book (directory of names → phone numbers). A central phone book company holds all the data. Now imagine tearing that phone book into thousands of pieces and distributing them to everyone in the city. Each person holds a slice of the directory. To find someone's number, you ask your neighbor, who asks their neighbor, until you reach someone who holds that slice of the book. No central company needed — but you can still find anyone.

In a DHT:
- Every peer in the network is assigned a random **node ID** (a large number)
- Every piece of data (e.g., "which peers have this torrent?") is assigned a **key** (a hash)
- Each piece of data is stored by the peers whose node IDs are numerically closest to the data's key
- To find data, you query a peer; they either have it or route your query to a peer closer to the key

**Properties of DHT:**
- **Truly decentralized** — no central server
- **Self-organizing** — peers join and leave; the network reorganizes
- **Efficient lookup** — finding data takes O(log N) hops for N peers

BitTorrent's DHT implementation is called **Kademlia**. When you add a magnet link (which contains just a hash), your client uses DHT to find peers entirely without a tracker.

---

## Blockchain: P2P for Trust

**Blockchain** is a P2P technology designed to solve a different problem: how can a group of parties who don't trust each other agree on a shared record of truth?

> **Analogy:** Imagine a town where nobody trusts the bank to keep honest records. Instead, every financial transaction is announced to the entire town, and every citizen writes it down in their own ledger. To fake a transaction, you'd have to alter the ledgers of most of the town simultaneously — practically impossible.

### How Blockchain Works (Simplified)

- A **blockchain** is a distributed ledger — a record of transactions
- New transactions are grouped into **blocks**
- Each block contains a cryptographic **hash of the previous block**, chaining them together
- The chain is distributed across thousands of **nodes** (peers)
- To add a new block, the network must reach **consensus** (e.g., via Proof of Work or Proof of Stake)
- Altering a past block would invalidate all subsequent blocks AND require consensus from the majority of the network — computationally prohibitive

**Applications of blockchain:**
- **Cryptocurrency** (Bitcoin, Ethereum) — decentralized digital currency with no central bank
- **Smart contracts** — self-executing programs stored on a blockchain (Ethereum)
- **Supply chain tracking** — tamper-proof record of product provenance
- **NFTs** (Non-Fungible Tokens) — ownership records for digital assets

**Limitations:**
- High energy consumption (Proof of Work)
- Slow compared to centralized databases
- Complex to develop and audit
- Not suitable for all use cases — often a centralized database is simpler and better

---

## P2P in Modern Technology

P2P principles appear in more places than file sharing:

| Technology | P2P Element |
|------------|------------|
| **WebRTC** | Powers browser-to-browser video calls (Zoom partially uses this) |
| **IPFS** (InterPlanetary File System) | A P2P file system for the web |
| **Tor** | Routes traffic through volunteer relays for anonymity |
| **Git** | Every clone is a full repository copy — no "master" copy required |
| **Steam game updates** | Uses P2P to distribute game patches efficiently |
| **Skype** (originally) | Used P2P for routing calls; now largely centralized on Azure |
| **Spotify** (originally) | Used P2P for music streaming; now fully CDN-based |
| **CDN edge caching** | Not true P2P, but inspired by P2P distribution principles |

---

## Netcat: A Simple P2P Demo

**Netcat** (`nc`) is a command-line tool that makes raw TCP or UDP connections. You can use it to demonstrate the essence of peer-to-peer communication: two computers talking directly to each other with no server in between.

We'll do a proper hands-on demo in [05 — Networking in Action](05-networking-in-action.md), but here's the concept:

**Terminal 1 (the "server" peer — listens for connections):**
```bash
nc -l 9999
```
This opens port 9999 and waits.

**Terminal 2 (the "client" peer — connects):**
```bash
nc localhost 9999
```
This connects to the listening peer.

Now, anything typed in either terminal appears in the other — a direct, unmediated connection. No HTTP, no web server, just raw TCP between two processes. This is the fundamental primitive that all P2P applications build on.

---

## Check Your Understanding

1. What is the main difference between the client-server model and P2P?
2. Why was Napster vulnerable to being shut down, even though it used P2P file transfers?
3. How does BitTorrent's "tit-for-tat" mechanism discourage leeching?
4. What problem does a DHT solve that a tracker cannot?
5. Why does BitTorrent's "rarest first" strategy make the overall network more resilient?

---

*Previous: [03 — Application Layer Protocols](03-application-layer-protocols.md)*
*Next: [05 — Networking in Action](05-networking-in-action.md)*
