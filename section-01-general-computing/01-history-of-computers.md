# 01 — The History of Computers

## Learning Objectives

By the end of this page, you will be able to:

- Describe the major eras in computing history, from mechanical calculators to smartphones
- Name key figures who shaped the development of computing
- Explain how each era built on the previous one
- Understand why this history matters when learning to build modern software

---

## Before Electricity: The Mechanical Age (1600s–1930s)

Long before silicon chips existed, humans needed to compute — to calculate large numbers quickly and accurately. The earliest "computers" were actually **people** hired to perform calculations by hand. As these needs grew, inventors built machines to help.

### Key Milestones

| Year | Event |
|------|-------|
| 1642 | Blaise Pascal builds the **Pascaline**, a mechanical adding machine using gears |
| 1837 | Charles Babbage designs the **Analytical Engine** — a general-purpose mechanical computer (never fully built in his lifetime) |
| 1843 | Ada Lovelace writes the first computer program — an algorithm for the Analytical Engine |
| 1890 | Herman Hollerith uses punch cards to tabulate the US Census |

### Charles Babbage — The Visionary

Babbage's **Analytical Engine** was a remarkable concept for its time. It had:
- A **"mill"** (what we now call a processor)
- A **"store"** (what we now call memory)
- Input via punch cards
- Conditional branching — the ability to follow different instructions based on results

It was never completed because the precision metalwork required was beyond what 19th-century manufacturing could produce.

> **Analogy:** Babbage's Analytical Engine is like a perfect architectural blueprint for a skyscraper drawn in 1840 — the design was sound, but the materials and tools needed to build it didn't exist yet.

### Ada Lovelace — The First Programmer

**Ada Lovelace** (1815–1852), daughter of the poet Lord Byron, collaborated with Babbage and wrote what is considered the **world's first computer program** — a method to compute Bernoulli numbers using the Analytical Engine. She also envisioned that such machines could go beyond pure calculation to compose music and handle symbols — ideas that were a century ahead of their time.

**Further Reading:**
- [Ada Lovelace — Britannica](https://www.britannica.com/biography/Ada-Lovelace)
- [Charles Babbage — Computer History Museum](https://www.computerhistory.org/babbage/)

---

## The Birth of Electronic Computing: 1930s–1950s

### Alan Turing — The Father of Computer Science

Before any electronic computer existed, mathematician **Alan Turing** (1912–1954) defined what a computer *could* do.

In 1936, he described the **Turing Machine** — a theoretical model showing that any problem solvable by an algorithm could be solved by a machine following simple rules. This is the conceptual foundation of every computer ever built.

During World War II, Turing led the effort to crack Nazi Germany's **Enigma** encryption machine at Bletchley Park. The work is estimated to have shortened the war by two years and saved millions of lives.

> **Key Point:** Turing never built a physical computer — he proved mathematically that machines *could* compute any computable problem. This gave engineers a target to aim for.

### ENIAC — The First General-Purpose Electronic Computer

| Property | Value |
|----------|-------|
| Year | 1945 |
| Location | University of Pennsylvania |
| Size | 1,800 square feet, 30 tons |
| Components | 18,000 vacuum tubes |
| Speed | ~5,000 additions per second |
| Programming method | Physically rewiring cables and setting switches |

ENIAC was primarily used to calculate artillery firing tables for the US Army. Calculations that took humans weeks were done in hours.

The **"programmers"** of ENIAC were a team of six women — Kay McNulty, Betty Jennings, Betty Snyder, Marlyn Wescoff, Fran Bilas, and Ruth Lichterman — who had previously computed ballistics trajectories by hand. They developed many foundational programming concepts while working with the machine.

### John von Neumann — The Architecture We Still Use

In 1945, **John von Neumann** proposed a computer architecture where both **data** and **instructions** are stored in the same memory. Before this, computers were often hardwired for specific tasks.

The **Von Neumann Architecture** has four components:

```
Input → Memory ↔ CPU → Output
                 ↑
            (instructions
             and data stored
             together)
```

Nearly every computer built since then — from laptops to smartphones — uses some form of this architecture.

**Further Reading:**
- [ENIAC — Computer History Museum](https://www.computerhistory.org/revolution/birth-of-the-computer/4/78)
- [Alan Turing — Biography](https://www.britannica.com/biography/Alan-Turing)
- [John von Neumann — Britannica](https://www.britannica.com/biography/John-von-Neumann)

---

## The Mainframe Era: 1950s–1970s

### What Is a Mainframe?

A **mainframe** is a large, powerful, expensive computer designed to serve many users simultaneously. Companies and governments would acquire a single mainframe and connect dozens of **"dumb terminals"** — keyboards and screens with no processing power of their own — to it.

> **Analogy:** A mainframe is like a large restaurant kitchen. All the cooking (computing) happens in one central place. The waiters (terminals) just take orders and deliver results — they can't cook anything themselves.

### Key Players

- **IBM** dominated the mainframe era. Their **System/360** (1964) was the first "family" of computers — different models that shared the same instruction set, meaning software written for one model ran on all others. This was revolutionary.
- **UNIVAC** — sold to the US Census Bureau in 1951, it was the first commercial computer in the United States

### How Work Was Done: Batch Processing

Early mainframes used **batch processing**:
1. Users wrote programs on **punch cards** — cards with holes punched to represent data
2. Cards were submitted to computer operators
3. Jobs were processed overnight in batches
4. Results were collected the next morning

> **Analogy:** Batch processing is like dropping off film at a photo lab. You submit your work, come back later, and get your results. You have no idea how it's going while it's running.

### Time-Sharing Changes Everything

By the 1960s, mainframes evolved to support **time-sharing** — multiple users interacting with the machine simultaneously. The computer switched between users so rapidly that each felt they had dedicated access.

Key developments:
- **MULTICS** (1960s) — an influential time-sharing OS developed jointly by MIT, Bell Labs, and GE
- **Unix** (1969, Bell Labs) — emerged from lessons learned building MULTICS; became one of the most influential operating systems ever created

**Further Reading:**
- [IBM System/360 — Computer History Museum](https://www.computerhistory.org/revolution/mainframe-computers/7/182)
- [The Unix Heritage](https://www.bell-labs.com/usr/dmr/www/hist.html)

---

## Minicomputers: 1960s–1970s

Mainframes cost millions of dollars and required entire rooms. **Minicomputers** changed the economics of computing.

### What Was a Minicomputer?

Still large by today's standards, but small enough to fit in a closet (not a building) and affordable for universities and mid-sized companies.

- **DEC PDP-8** (1965) — the first commercially successful minicomputer; the size of a large refrigerator, cost ~$18,000
- **DEC PDP-11** (1970) — extremely popular in universities; Unix was originally written for this machine
- **DEC VAX** (1977) — powerful and popular throughout the late 1970s and 1980s

Minicomputers brought computing to engineers, scientists, and researchers who couldn't justify mainframe costs. Many of the programmers who shaped modern software cut their teeth on minicomputers.

---

## The Personal Computer Revolution: 1970s–1990s

### The Microprocessor: Computing on a Chip

The critical enabler of personal computing was the **microprocessor** — an entire CPU on a single silicon chip.

- **1971** — Intel releases the **4004**, the first commercial microprocessor, with 2,300 transistors
- **1974** — Intel **8080** becomes the foundation for early hobbyist computers
- **1978** — Intel **8086** establishes the **x86 architecture** that still runs most Windows PCs today

> **Perspective:** The Intel 4004 performed about 92,000 operations per second. A modern smartphone processor performs billions per second. That's roughly a 10-million-fold improvement in 50 years.

### The Hobbyist Era

- **1975** — The **Altair 8800** kit computer appears on the cover of *Popular Electronics*
  - No keyboard, no screen, no software — you programmed it by flipping switches
  - It sparked enormous excitement among hobbyists
- Two young programmers named **Bill Gates** and **Paul Allen** write a version of BASIC for it, founding **Microsoft**
- The **Homebrew Computer Club** in Silicon Valley became a gathering point for enthusiasts; Steve Jobs and Steve Wozniak were members

### Apple: Making Computers for People

- **1976** — Steve Wozniak designs the **Apple I**; Steve Jobs persuades him to sell it as a product, and they found **Apple Computer**
- **1977** — The **Apple II** ships with a keyboard, color display, and floppy drive — the first truly "personal" computer for ordinary people
- **1981** — The **Apple III** and **Lisa** follow; the Lisa introduces the GUI but is too expensive
- **1984** — The **Macintosh** launches with the now-famous [1984 Super Bowl ad](https://www.youtube.com/watch?v=2zfqw8nhUwA)
  - First mainstream computer with a **graphical user interface (GUI)** and a mouse
  - "The computer for the rest of us"

### IBM PC and the Clone Wars

- **1981** — **IBM** releases the **IBM PC**
  - Uses an off-the-shelf **Intel 8088** chip
  - Uses **Microsoft's PC-DOS** (MS-DOS) as its operating system
  - IBM publishes the hardware architecture openly — a decision with enormous consequences

- Because IBM's architecture was open, other companies (Compaq, HP, Acer, Dell) built **IBM-compatible clones** that ran the same software
- This created massive price competition, driving PC prices down dramatically
- **Microsoft** had retained the OS license and sold MS-DOS to all clone makers — transforming the company into one of the most powerful in the world

> **Key Point:** IBM's open architecture decision democratized personal computing by creating competition, but it also meant IBM lost control of the market it created. Microsoft and Intel were the real winners.

**Further Reading:**
- [Personal Computer Revolution — Computer History Museum](https://www.computerhistory.org/revolution/personal-computers/17/297)
- [The IBM PC Story](https://www.ibm.com/history/personal-computer)
- [Apple History](https://www.apple.com/timeline/)

---

## The Internet Age: 1990s–2000s

Once millions of people had PCs, connecting them became the next frontier.

### Key Events

| Year | Event |
|------|-------|
| 1969 | ARPANET, the first computer network, connects four universities |
| 1983 | ARPANET adopts TCP/IP — the protocol that still runs the Internet |
| 1991 | Tim Berners-Lee invents the **World Wide Web** at CERN |
| 1993 | **Mosaic** (later Netscape) — first graphical web browser — makes the web accessible |
| 1994 | Amazon and Netscape launch |
| 1995 | Windows 95 ships with built-in Internet support; eBay launches |
| 1998 | **Google** launches, transforming information discovery |
| 2001 | Wikipedia launches |
| 2004 | **Facebook** launches |

### The Web Transforms Computing

The Internet changed computers from tools for computation into tools for **communication, commerce, and collaboration**. The "killer app" of the 1990s wasn't spreadsheets or word processors — it was email and the web browser.

> **Analogy:** The Internet is like adding roads, postal services, and telephone lines between all the world's computers at once. Suddenly the value of each computer multiplied enormously because it could communicate with all the others.

**Further Reading:**
- [History of the Internet — Wikipedia](https://en.wikipedia.org/wiki/History_of_the_Internet)
- [Tim Berners-Lee and the Web](https://www.w3.org/People/Berners-Lee/)

---

## The Mobile Revolution: 2007–Present

### Smartphones Redefine Computing

- **2007** — Apple releases the **iPhone** — a touchscreen computer that fits in your pocket, combining phone, camera, music player, and Internet browser
- **2008** — **Google releases Android**, an open-source mobile OS
- **2010** — Smartphones and tablets begin outselling desktop computers

Smartphones brought computing to people who never owned a PC, especially in developing nations where mobile leapfrogged desktop entirely.

### The Cloud Era

Rather than running all software locally, **cloud computing** lets you run software on servers accessed over the Internet.

- **Amazon Web Services (AWS)** launches in 2006
- **Google Cloud** and **Microsoft Azure** follow
- Applications like Gmail, Netflix, Spotify, and Google Docs run "in the cloud" — your device is the window, not the engine

> **Key Point:** Today, "the computer" is increasingly a network of machines. When you stream a movie on Netflix, thousands of servers across multiple continents collaborate to deliver that video to your screen in real time.

---

## Key People Reference

| Person | Contribution |
|--------|-------------|
| Ada Lovelace (1815–1852) | First programmer |
| Alan Turing (1912–1954) | Theory of computation, AI foundations, Enigma |
| John von Neumann (1903–1957) | Stored-program computer architecture |
| Grace Hopper (1906–1992) | First compiler; coined "debugging" |
| Dennis Ritchie (1941–2011) | Created C language; co-created Unix |
| Ken Thompson (b. 1943) | Co-created Unix; later created Go |
| Bill Gates (b. 1955) | Microsoft; DOS; Windows |
| Steve Jobs (1955–2011) | Apple; Macintosh; iPhone |
| Steve Wozniak (b. 1950) | Apple I and II hardware design |
| Linus Torvalds (b. 1969) | Created Linux (1991) |
| Tim Berners-Lee (b. 1955) | Invented the World Wide Web |

---

## Check Your Understanding

1. What was the key innovation of von Neumann architecture, and why do we still use it today?
2. What made the IBM PC's "open architecture" such a pivotal decision — and who benefited most from it?
3. How did the microprocessor enable the personal computer revolution?
4. What is the difference between batch processing and time-sharing?
5. How did the Internet change the *purpose* of a personal computer?

---

*Next: [02 — Input, Output, and Processing](02-input-output-processing.md)*
