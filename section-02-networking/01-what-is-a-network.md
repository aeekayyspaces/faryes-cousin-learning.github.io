# 01 — What Is a Network? The History of the Internet

## Learning Objectives

By the end of this page, you will be able to:

- Define a computer network and explain why networks exist
- Describe the types of networks (LAN, WAN, MAN) and where you encounter each
- Trace the history of the Internet from ARPANET to today
- Explain what the Internet actually is (and isn't)
- Name the key organizations and decisions that shaped the modern Internet

---

## What Is a Computer Network?

A **computer network** is two or more computers connected together in a way that allows them to share data and resources.

Networks exist because isolated computers are limited. The moment you connect computers together, you multiply their usefulness enormously:

- Share files between machines without carrying a USB drive
- Send messages to people across the world instantly
- Access information stored on servers anywhere on Earth
- Collaborate on documents with people in other time zones
- Stream video from a data center thousands of miles away

> **Analogy:** A single computer is like a very capable person locked in a room with no phone, no mail, and no visitors. Incredibly smart, but isolated. A network is the postal system, telephone network, and highway system rolled into one — connecting every computer to every other computer and multiplying what's possible for all of them.

---

## Types of Networks

Networks are often described by their geographic scope:

### PAN — Personal Area Network

- **Scale:** A few meters
- **Examples:** Bluetooth connecting your phone to your headphones, AirDrop between two MacBooks, a smartwatch talking to your phone
- **Technology:** Bluetooth, NFC, USB

### LAN — Local Area Network

- **Scale:** A building or campus
- **Examples:** Your home Wi-Fi, a school's network, an office network
- **Technology:** Ethernet cables, Wi-Fi (802.11 a/b/g/n/ac/ax)
- **Speed:** Typically 100 Mbps to 10 Gbps

This is the network you're on when you connect to your home router. Your laptop, phone, smart TV, and printer are all on the same LAN. They can communicate directly with each other without going through the Internet.

### MAN — Metropolitan Area Network

- **Scale:** A city or campus cluster
- **Examples:** A university with multiple buildings, a city's public Wi-Fi mesh, cable TV infrastructure
- **Technology:** Fiber optic cables, wireless towers

### WAN — Wide Area Network

- **Scale:** Countries, continents, the world
- **Examples:** The Internet itself, a corporation's private network connecting offices globally
- **Technology:** Fiber optic undersea cables, satellite links, leased lines

> **Key Point:** The **Internet** is the world's largest WAN — a global network of networks. When data travels from your laptop to a server in another country, it passes through many different networks, each owned by different organizations.

---

## Network Topologies

**Topology** describes how devices in a network are physically or logically connected to each other.

### Star Topology

```
    Device A
       |
Device B — [SWITCH/ROUTER] — Device C
       |
    Device D
```

All devices connect to a central hub or switch. Most modern LANs use this. If the switch fails, all devices lose connectivity, but one device failing doesn't affect others.

**Used by:** Home networks, office LANs

### Mesh Topology

```
A — B — C
|  ╲|╱  |
D — E — F
```

Every device connects to multiple others. If one path fails, data can reroute through another. More resilient but more expensive.

**Used by:** Military networks, the Internet's backbone, modern Wi-Fi mesh systems (like Eero or Google Nest)

### Bus Topology (Historical)

All devices share a single communication line. Easy and cheap, but if the line fails, everything goes down. Mostly obsolete.

### Ring Topology (Historical)

Devices form a circle; data passes around the ring. Used in older token ring networks. Mostly obsolete for LANs.

> **Key Point:** The Internet itself is roughly a mesh — data between your computer and a server might travel through dozens of different routers via different paths, and if one path is congested or broken, traffic automatically routes around it. This resilience was one of ARPANET's original design goals.

---

## The History of the Internet

### The Cold War Origins: 1957–1969

The story of the Internet begins with a satellite and fear.

In 1957, the Soviet Union launched **Sputnik** — the world's first artificial satellite. The United States government, alarmed by the Soviet technological achievement, created **ARPA** (Advanced Research Projects Agency) in 1958 to accelerate American scientific and technological development.

One concern at the time: the US military's communications network was centralized. A single nuclear strike on Washington D.C. could cut off the entire command and control system. ARPA funded research into a **decentralized** communication network — one with no single point of failure, where messages could find alternative paths even if parts of the network were destroyed.

### ARPANET: 1969

On **October 29, 1969**, the first message was sent over **ARPANET** — the first packet-switched network, connecting four universities:

- University of California, Los Angeles (UCLA)
- Stanford Research Institute
- University of California, Santa Barbara
- University of Utah

The message was supposed to be "LOGIN" — but the system crashed after the first two letters. The first message ever sent on what would become the Internet was **"LO"**.

By 1972, ARPANET had 23 nodes. Email was invented in 1971 by Ray Tomlinson (who chose the `@` symbol to separate user from host — a convention we still use today).

### Packet Switching: The Key Innovation

Traditional telephone calls used **circuit switching** — a dedicated physical connection was established for the duration of the call. This was reliable but inefficient; the circuit was reserved even when nobody was talking.

ARPANET used **packet switching** — data is broken into small chunks (packets), each labeled with source and destination addresses. Packets travel independently through the network, possibly by different routes, and are reassembled at the destination.

> **Analogy:** Circuit switching is like booking a private highway lane for your entire trip. Packet switching is like mailing a book by tearing out the pages, mailing each page in a separate envelope, and having the recipient reassemble the book. Each envelope finds its own route, and if one gets delayed, the others continue on. This turns out to be far more efficient and resilient.

### The Protocols: TCP/IP Takes Over — 1983

By the late 1970s, multiple incompatible networks existed. They couldn't talk to each other because they used different protocols.

**Vint Cerf** and **Bob Kahn** designed **TCP/IP** (Transmission Control Protocol / Internet Protocol) — a common language for all networks to communicate. On **January 1, 1983** — known as "**Flag Day**" — ARPANET officially switched to TCP/IP. Any network anywhere in the world that adopted TCP/IP could now communicate with any other.

This is the moment the Internet was technically born. TCP/IP is still the foundation of all Internet communication today.

### The Domain Name System: 1983

Early network users navigated by **IP addresses** (numerical addresses like `192.168.1.1`). This quickly became unmanageable as networks grew.

In 1983, **DNS** (Domain Name System) was introduced — a distributed directory that translates human-readable names like `google.com` into IP addresses. We'll cover DNS in detail in Section 3.

### NSFNet and the Commercialization: 1985–1995

ARPANET was a research network. In 1985, the **National Science Foundation** created **NSFNet** to connect universities and research institutions with faster links. NSFNet became the backbone of the emerging Internet.

In **1991**, the NSF lifted restrictions on commercial use of the Internet. This opened the floodgates.

| Year | Event |
|------|-------|
| 1991 | Tim Berners-Lee invents the **World Wide Web** at CERN |
| 1993 | **Mosaic** browser released — first graphical web browser |
| 1994 | **Netscape** Navigator launches; Amazon and Yahoo! launch |
| 1995 | **NSFNet** decommissioned; Internet becomes fully commercial; eBay launches; Windows 95 includes Internet support |
| 1996 | ~36 million Internet users worldwide |
| 1998 | **Google** launches; ICANN formed to manage domain names |
| 1999 | ~280 million users; dot-com boom at peak |

### The Dot-Com Boom and Bust: 1995–2001

The commercial Internet triggered a gold rush. Companies with ".com" in their name could raise millions of dollars regardless of whether they had a viable business. Pets.com spent $11.8 million on Super Bowl ads. Webvan tried to replace all grocery stores.

In 2000–2001, the **dot-com bubble burst**. The NASDAQ lost 78% of its value. Hundreds of Internet companies failed. But the infrastructure, the users, and the real companies (Amazon, Google, eBay) survived and became stronger.

> **Key Point:** The dot-com bust destroyed companies but not the Internet itself. When the dust settled, the Internet had more users, more infrastructure, and more viable business models than before the crash.

### Web 2.0 and Social Media: 2003–2010

The second wave of the Web was about **user-generated content** and **social interaction**:

| Year | Launch |
|------|--------|
| 2003 | LinkedIn, MySpace, WordPress |
| 2004 | Facebook, Flickr, Gmail |
| 2005 | YouTube, Reddit |
| 2006 | Twitter |
| 2007 | iPhone + mobile Internet |
| 2009 | WhatsApp |
| 2010 | Instagram |

By 2010: ~2 billion Internet users.

### Mobile Internet: 2007–Present

The iPhone (2007) and Android (2008) put the Internet in everyone's pocket. By 2016, **mobile surpassed desktop** as the primary way people access the Internet globally.

Mobile changed the Internet from something you sit down to use, into something that's constantly with you. This created entirely new categories of apps, businesses, and behaviors.

### The Cloud Era: 2006–Present

**Cloud computing** means renting computing resources — servers, storage, databases — from large providers instead of owning physical hardware.

- **Amazon Web Services (AWS)** launched in 2006
- **Google Cloud** and **Microsoft Azure** followed
- Today, the majority of websites, apps, and services run on cloud infrastructure

When you use Netflix, your video is stored on AWS servers. When you use Gmail, your emails live on Google's servers. The "cloud" is just other people's computers — but those computers are managed, scaled, and maintained by specialists, making them vastly more reliable than most organizations' own infrastructure.

### The Internet Today

As of 2024:
- **~5.4 billion Internet users** — over 67% of the world's population
- **~1.5 billion websites** (fewer actively maintained)
- **~330 billion emails** sent per day
- **~500 hours of video** uploaded to YouTube every minute
- **~5 billion Google searches** per day
- Internet traffic measured in **exabytes per month** (1 exabyte = 1 billion GB)

The Internet runs on hundreds of thousands of miles of **undersea fiber optic cables** connecting continents, plus satellite networks (Starlink, etc.) filling coverage gaps.

**Further Reading:**
- [History of the Internet — Wikipedia](https://en.wikipedia.org/wiki/History_of_the_Internet)
- [Internet History Timeline — CNET](https://www.cnet.com/tech/services-and-software/internet-history-timeline/)
- [How the Internet Works — Khan Academy](https://www.khanacademy.org/computing/computers-and-the-internet)
- [A Brief History of the Internet — Internet Society](https://www.internetsociety.org/internet/history-internet/brief-history-internet/)

---

## Check Your Understanding

1. What was the original motivation behind ARPANET? How does that motivation explain its design?
2. What is the difference between a LAN and a WAN? Which one are you on when you use your home Wi-Fi?
3. What is packet switching and why is it better than circuit switching for a large network?
4. Why was January 1, 1983 ("Flag Day") significant for the Internet?
5. What is the difference between the Internet and the World Wide Web?

---

*Next: [02 — How Data Travels: TCP/IP and Packets](02-how-data-travels.md)*
