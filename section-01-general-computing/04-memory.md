# 04 — Memory: How Computers Remember

## Learning Objectives

By the end of this page, you will be able to:

- Describe the memory hierarchy and explain why it exists
- Distinguish between volatile and non-volatile memory
- Explain how RAM, cache, registers, and storage work together
- Understand what virtual memory is and why it matters
- Trace what happens in memory when you open and run a program

---

## Why Memory Is a Hierarchy

There's a fundamental trade-off in memory design:

| Property | Fast Memory | Slow Memory |
|----------|-------------|-------------|
| Access speed | Very fast | Slow |
| Cost per GB | Extremely expensive | Cheap |
| Capacity per chip | Very small | Very large |
| Physical location | Inside the CPU | On the motherboard / external |

No single type of memory can be simultaneously fast, large, and cheap. So computers use a **hierarchy** of different memory types, with each layer serving a different purpose.

> **Analogy:** Imagine a chef working at a restaurant.
> - **Registers** = what the chef is holding in their hands right now
> - **L1/L2 Cache** = ingredients on the prep counter within arm's reach
> - **L3 Cache** = the refrigerator a few steps away
> - **RAM** = the storage room down the hall
> - **SSD/HDD** = a warehouse across town
> - **Cloud/Tape** = an archive in another city
>
> The chef works fastest with what's in hand. Fetching from the warehouse takes much longer. Good chefs (and good CPUs) plan ahead to keep what they need close by.

---

## Layer 1: Registers

**Registers** are the fastest memory in a computer, located directly inside the CPU.

- **Size:** Tiny — typically 64 bits (8 bytes) each, with ~100–500 registers total per core
- **Speed:** Sub-nanosecond (< 1 billionth of a second)
- **Volatile:** Yes — contents are lost when power is off
- **Managed by:** The CPU itself (and the compiler that writes programs)

Registers hold the data the CPU is *actively using right now* — the current instruction, intermediate calculation results, memory addresses being worked with.

You don't interact with registers when programming in high-level languages like JavaScript or Python — the compiler or interpreter handles this automatically.

---

## Layer 2: Cache (L1, L2, L3)

**CPU cache** is a small, very fast memory built directly into the CPU die (the chip itself). Its entire purpose is to reduce how often the CPU has to wait for data from the slower RAM.

### How Cache Works

The CPU uses a strategy called **locality of reference**:
- **Temporal locality:** If you accessed a piece of data recently, you'll probably need it again soon
- **Spatial locality:** If you accessed data at a memory address, you'll probably need data at nearby addresses soon

The cache stores recently accessed data and data near recently accessed addresses, betting that the CPU will need it again.

### Cache Levels

| Level | Size | Speed | Location |
|-------|------|-------|----------|
| L1 Cache | 32–512 KB per core | ~1 ns | Inside each core |
| L2 Cache | 256 KB – 8 MB per core | ~4 ns | Inside each core or nearby |
| L3 Cache | 8 – 64 MB, shared | ~10–40 ns | Shared across all cores |
| RAM | 8 – 128 GB | ~60–100 ns | Motherboard |

### Cache Hits and Misses

- **Cache hit:** The CPU finds the needed data in cache — fast!
- **Cache miss:** The data isn't in cache; the CPU must fetch from RAM — slow

Modern CPUs achieve cache hit rates of ~95–99% for many workloads, which is why cache size and design matter so much for performance.

> **Key Point:** A CPU running at 4 GHz can complete an operation in 0.25 nanoseconds. A RAM access takes ~70 nanoseconds — that's 280 CPU cycles of waiting. Cache exists to prevent this waiting.

---

## Layer 3: RAM (Random Access Memory)

**RAM** (also called **main memory**) is the primary working memory of your computer. When you run programs, both the program's code and the data it's working with live in RAM.

### Key Properties

- **Volatile:** Contents are erased when power is removed
- **Random access:** Any location in RAM can be read or written equally fast (unlike a hard drive that needs to physically move to read data)
- **Byte-addressable:** Every byte in RAM has a unique address the CPU can reference directly

### How RAM Is Organized

RAM can be thought of as a massive grid of tiny storage cells, each holding one bit (0 or 1). Modern RAM is organized in bytes (8 bits) and given addresses:

```
Address:  0x0000  0x0001  0x0002  0x0003  ...
Value:    [01001010] [11001101] [00000001] [10110100] ...
```

When the CPU needs the value at address `0x0002`, it sends that address to RAM and RAM sends back `00000001`.

### RAM Capacity and Performance

| RAM | Suitable for |
|-----|-------------|
| 4 GB | Very basic tasks; will feel slow with modern OS |
| 8 GB | Light use: web browsing, documents |
| 16 GB | Good for most users: multitasking, light development |
| 32 GB | Power users, developers, light video editing |
| 64 GB+ | Video editing, machine learning, heavy virtualization |

### RAM Speed

RAM speed is measured in **MHz** (or **MT/s** — megatransfers per second). Common speeds: DDR4-3200, DDR5-4800, DDR5-6000. Faster RAM reduces the time the CPU waits on memory access, improving performance — though the gains are often subtle for everyday tasks.

---

## Layer 4: Storage (SSD and HDD)

**Storage** is persistent memory — data survives when power is removed. This is where your files, installed programs, and operating system live when they're not actively being used.

### SSD (Solid State Drive)

- Uses **NAND flash memory** — no moving parts
- **Read speed:** 500 MB/s (SATA) to 7,000+ MB/s (NVMe)
- **Write speed:** Similar to read, slightly lower
- **Durability:** High — survives drops and vibration
- **Price:** More expensive per GB than HDD, but declining rapidly
- **Lifespan:** Measured in TBW (Terabytes Written); consumer SSDs typically 150–600 TBW

### HDD (Hard Disk Drive)

- Uses **spinning magnetic platters** with a moving read/write head
- **Read/write speed:** 80–160 MB/s typical
- **Capacity:** Often larger and cheaper per GB than SSD
- **Durability:** Fragile — the moving parts can fail if dropped
- **Used for:** Bulk storage, backups, NAS (Network Attached Storage)

> **Analogy:** An SSD is like a digital photo — instant to look at, no moving parts. An HDD is like a vinyl record — the needle has to physically move to find the right track.

### NVMe vs. SATA

Modern SSDs connect via **NVMe** (over PCIe) rather than the older **SATA** interface, providing much higher speeds. Most new laptops and desktops use NVMe SSDs.

---

## Virtual Memory

What happens when your programs need more RAM than you have?

**Virtual memory** is a technique where the operating system uses part of the storage drive as an extension of RAM, creating a large "virtual" address space for programs.

### How It Works

1. The OS divides memory into fixed-size chunks called **pages** (typically 4 KB)
2. Pages that haven't been used recently are **"swapped out"** — written from RAM to a special area on the SSD/HDD (called the **swap file** or **page file**)
3. When that data is needed again, it's **swapped in** — read back from disk into RAM
4. If necessary, a different page is swapped out to make room

### The Downside

Because storage is 100–1000x slower than RAM, heavy use of virtual memory (called **thrashing**) makes a computer feel very slow. This is why:
- A computer with 4 GB RAM and Chrome open with 50 tabs becomes sluggish
- Upgrading RAM often has a dramatic effect on performance

> **Key Point:** Virtual memory prevents your programs from *crashing* when RAM is full. But it doesn't make things *fast* — it just keeps things running.

---

## What Happens When You Open an Application

Let's trace the memory journey of opening an app (say, a text editor):

```
1. You double-click the icon
   └── Mouse click → OS receives input event

2. OS finds the app on storage (SSD)
   └── Storage access: app binary (executable file) located

3. OS loads the app from storage into RAM
   └── Executable code + initial data copied to RAM
   └── Takes milliseconds to seconds depending on app size

4. CPU begins executing the app's code
   └── Instructions flow: RAM → L3 Cache → L2 Cache → L1 Cache → CPU registers

5. App opens a window
   └── Window data sent to GPU for rendering
   └── GPU writes pixel data to screen

6. You type text
   └── Keystrokes → OS → app → stored in RAM as a buffer
   └── Buffer contents rendered to screen by GPU

7. You save the file
   └── Buffer (from RAM) written to storage (SSD/HDD)
   └── Data now persistent — survives power off
```

---

## Memory in Context: A Summary Table

| Memory Type | Speed | Size | Volatile? | Managed By |
|-------------|-------|------|-----------|------------|
| Registers | < 1 ns | Bytes | Yes | CPU / Compiler |
| L1 Cache | ~1 ns | KB | Yes | CPU hardware |
| L2 Cache | ~4 ns | KB–MB | Yes | CPU hardware |
| L3 Cache | ~10–40 ns | MB | Yes | CPU hardware |
| RAM | ~60–100 ns | GB | Yes | OS + CPU |
| SSD (NVMe) | ~0.1 ms | 100s GB | No | OS + File System |
| HDD | ~5–10 ms | TBs | No | OS + File System |
| Cloud Storage | ~100+ ms | Unlimited | No | Cloud provider |

---

## Check Your Understanding

1. Why do computers use a hierarchy of memory rather than just using one type for everything?
2. What is the difference between volatile and non-volatile memory? Give an example of each.
3. What is a cache hit? What happens on a cache miss?
4. Why does heavy use of virtual memory make a computer slow?
5. List the path data travels from storage to the CPU registers, in order from slowest to fastest.

---

*Previous: [03 — The CPU](03-the-cpu.md)*
*Next: [05 — Operating Systems](05-operating-systems.md)*
