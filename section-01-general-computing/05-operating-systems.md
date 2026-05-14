# 05 — Operating Systems

## Learning Objectives

By the end of this page, you will be able to:

- Explain what an operating system is and what problems it solves
- Describe the role of the kernel
- List the major responsibilities of an OS (process, memory, file, device management)
- Trace the history of major operating systems
- Understand the difference between a CLI and a GUI
- Name common file systems and what they do

---

## What Is an Operating System?

An **Operating System (OS)** is software that manages all of a computer's hardware and provides a platform for other software to run on.

Without an OS, every application would need to include code to talk directly to the hard drive, the keyboard, the screen, the network card — all the hardware. Every developer would have to reinvent those wheels from scratch, and applications from different vendors would conflict constantly.

> **Analogy:** Think of the OS as the management staff of a large office building. Tenants (applications) don't worry about electricity, plumbing, security, or elevators — the building management handles all that. Each tenant can focus on their own work. If two tenants need the same conference room (CPU time), management schedules them so they don't conflict.

The OS sits between the **hardware** and the **applications**:

```
┌─────────────────────────────────────┐
│         APPLICATIONS                │
│  (Browser, Word, Games, etc.)       │
├─────────────────────────────────────┤
│         OPERATING SYSTEM            │
│  (Windows, macOS, Linux)            │
├─────────────────────────────────────┤
│         HARDWARE                    │
│  (CPU, RAM, SSD, GPU, Network)      │
└─────────────────────────────────────┘
```

---

## The Kernel

The **kernel** is the core of the operating system — the part that runs continuously and has direct access to hardware. Everything else in the OS runs on top of the kernel.

### What the Kernel Does

- **Manages the CPU** — decides which process runs when
- **Manages memory** — allocates and protects RAM for each process
- **Manages devices** — communicates with hardware via **device drivers**
- **Manages the file system** — handles reading and writing to storage
- **Manages security** — enforces permissions; prevents programs from accessing memory they don't own

### Kernel Space vs. User Space

The OS separates memory into two zones:

- **Kernel space:** Where the kernel runs. Applications cannot access this directly. Direct hardware access lives here.
- **User space:** Where applications run. If a program crashes here, the OS can isolate and kill it without taking down the whole system.

> **Key Point:** When an application needs something from hardware (reading a file, sending network data), it makes a **system call** — a formal request to the kernel. The kernel does the work and returns the result. Apps never touch hardware directly.

---

## What an OS Actually Does

### 1. Process Management

A **process** is a running program. Your OS might have hundreds of processes running simultaneously (browser tabs, background services, antivirus, system tasks).

The OS uses a **scheduler** to divide CPU time between processes:
- On a single-core CPU, it rapidly switches between processes — so fast that it appears simultaneous
- On multi-core CPUs, true parallel execution occurs
- Each process gets a **priority** level; higher priority = more CPU time

**Process states:**
```
NEW → READY → RUNNING → WAITING → READY (cycle)
                    └──────────────→ TERMINATED
```

### 2. Memory Management

The OS:
- Allocates RAM to processes when they start
- Ensures each process can only read/write its own memory (isolation)
- Manages virtual memory (swapping pages to/from disk)
- Reclaims memory when a process ends

Memory isolation is critical for security. When your browser's tab crashes, it shouldn't be able to read the memory of your password manager.

### 3. File System Management

The OS provides a unified way to read and write data:
- **Files** are named collections of data stored on a device
- **Directories (folders)** organize files in a hierarchy
- The OS tracks where every file's data physically lives on storage

The **file system** is the format used to organize data on a storage device:

| File System | OS | Notes |
|-------------|-----|-------|
| **NTFS** | Windows | Standard for Windows; supports large files, permissions |
| **FAT32** | Universal | Old Windows; limited to 4 GB max file size; used on USB drives |
| **exFAT** | Universal | Modern USB/SD cards; no 4 GB limit |
| **ext4** | Linux | Standard Linux file system |
| **APFS** | macOS/iOS | Apple's modern file system; optimized for SSDs |
| **HFS+** | macOS (older) | Apple's older format, still found on some drives |
| **ZFS** | Linux/BSD | Advanced; used in servers; excellent data integrity |

### 4. Device Driver Management

**Device drivers** are small programs that translate generic OS commands into hardware-specific instructions.

Example: When you print a document, the OS says "send this data to the printer." The printer's driver translates that into the specific protocol the printer understands. This is why installing a new printer requires installing its driver.

### 5. User Interface

The OS provides a way for users to interact with the computer:

- **CLI (Command Line Interface):** Text-based. You type commands, the OS executes them and returns text output.
- **GUI (Graphical User Interface):** Visual. Windows, icons, menus, and a mouse pointer.

Most modern OS provide both. Developers often prefer the CLI for its power and automation capabilities; everyday users generally use the GUI.

---

## A History of Operating Systems

### The Pre-OS Era: 1940s–1950s

Early computers had no OS. Each program was loaded manually, ran to completion, and then the machine was re-loaded for the next job. **Operators** physically wired circuits and loaded punch cards.

### Batch Processing OS: 1950s–1960s

Simple software to automatically feed jobs (batches of punch cards) to the computer and handle output. No user interaction while jobs ran.

### Time-Sharing OS: 1960s–1970s

Multiple users could interact with the computer simultaneously:
- **CTSS** (1961, MIT) — one of the first time-sharing systems
- **MULTICS** (1969) — ambitious multi-user OS; too complex but highly influential

### Unix: 1969

**Unix** was created at Bell Labs by **Ken Thompson** and **Dennis Ritchie** as a simpler alternative to MULTICS. Key innovations:
- Written in **C** (a portable language), making Unix portable to different hardware
- **Everything is a file** — hardware devices, processes, network connections all exposed as files
- Small tools that do one thing well, combined via **pipes**
- Multi-user and multi-tasking from the start

Unix became the foundation for macOS, Linux, Android, iOS, and many server operating systems.

### MS-DOS: 1981

**MS-DOS** (Microsoft Disk Operating System) was a simple, single-user, command-line OS that shipped with the IBM PC. It was:
- **Single-tasking** — ran one program at a time
- **Text-based** — no graphics, just text commands
- **Hugely influential** — introduced most of a generation to personal computing

Key commands: `DIR`, `CD`, `COPY`, `DEL`, `TYPE` — many still work in Windows today.

### Windows: 1985–Present

Microsoft built a **GUI on top of DOS**, eventually evolving it into a full OS:

| Version | Year | Key Feature |
|---------|------|-------------|
| Windows 1.0 | 1985 | First GUI; ran on top of DOS |
| Windows 3.1 | 1992 | First widely adopted version |
| Windows 95 | 1995 | Start menu, taskbar, built-in Internet |
| Windows XP | 2001 | Stable, beloved; used for over a decade |
| Windows Vista | 2007 | Controversial; performance issues |
| Windows 7 | 2009 | Widely praised; still used by many |
| Windows 8 | 2012 | Touch-focused; broadly unpopular |
| Windows 10 | 2015 | Return to form; now most used Windows version |
| Windows 11 | 2021 | Modern design; stricter hardware requirements |

### macOS: 2001–Present

Apple's **macOS** is a Unix-based OS (built on **Darwin**, which is based on BSD Unix). Key milestones:

- **Mac OS X 10.0** (2001) — combined classic Mac interface with Unix underpinnings
- **10.5 Leopard** (2007) — Time Machine, Boot Camp
- **10.9 Mavericks** (2013) — Free upgrades began
- **macOS Big Sur** (2020) — First version for Apple Silicon (ARM)
- **macOS Sequoia** (2024) — Current version as of 2024

macOS is known for its polished interface, tight hardware-software integration, and Unix terminal.

### Linux: 1991–Present

**Linux** is an open-source Unix-like kernel created by **Linus Torvalds** in 1991 as a personal project. The **GNU Project** (started 1983 by Richard Stallman) provided the tools and utilities that turned the Linux kernel into a complete OS.

Linux is not a single OS but a **family of distributions** (distros):

| Distro | Who It's For |
|--------|-------------|
| **Ubuntu** | Beginners; most popular desktop Linux |
| **Debian** | Stable; base for Ubuntu and many others |
| **Fedora** | Cutting-edge; Red Hat upstream |
| **Arch Linux** | Advanced users; you build it yourself |
| **Mint** | User-friendly; great for Windows migrants |
| **Kali Linux** | Security research and penetration testing |
| **Ubuntu Server / Debian** | Web servers, cloud computing |

**Linux runs the world's infrastructure:**
- ~96% of the world's top 1 million web servers
- All 500 of the world's fastest supercomputers
- Android (based on the Linux kernel)
- Google, Amazon, and Facebook's server infrastructure

> **Key Point:** Even if you never use Linux on a desktop, as a developer you will almost certainly deploy software that runs on Linux servers.

### Android and iOS

- **Android** (2008, Google) — based on the Linux kernel; runs on 70%+ of smartphones globally; open-source core, manufacturer-customized
- **iOS** (2007, Apple) — based on Darwin (same Unix base as macOS); runs exclusively on Apple hardware; known for tight hardware-software integration and security

**Further Reading:**
- [The Unix Story — Bell Labs](https://www.bell-labs.com/usr/dmr/www/hist.html)
- [Linux History — The Linux Foundation](https://www.linuxfoundation.org/blog/linux-kernel-history/)
- [History of Windows — Microsoft](https://www.microsoft.com/en-us/windows/windows-history)
- [macOS Version History — Apple](https://support.apple.com/en-us/100100)

---

## The Shell: Your Gateway to the OS

A **shell** is a program that gives you a text-based interface to the OS. You type commands, the shell interprets them, asks the kernel to do things, and shows you the results.

Common shells:

| Shell | Platform | Notes |
|-------|----------|-------|
| **cmd.exe** | Windows | Classic Windows command prompt; limited |
| **PowerShell** | Windows | Powerful; object-based; modern choice |
| **bash** | Linux/macOS | The most common Unix shell; default on Ubuntu |
| **zsh** | macOS/Linux | bash-compatible with extras; default on macOS since 2019 |
| **fish** | Linux/macOS | User-friendly; good autocompletion |

We'll go deep on shell usage in [06 — The Command Line](06-command-line.md).

---

## Check Your Understanding

1. What would happen if there were no operating system — how would you write a program to save a file?
2. What is the difference between kernel space and user space, and why does that separation matter?
3. Why does installing a new printer require a driver?
4. What does it mean that Linux "runs the world's infrastructure"? Why should a developer care?
5. What is the difference between a CLI and a GUI? When might a developer prefer the CLI?

---

*Previous: [04 — Memory](04-memory.md)*
*Next: [06 — The Command Line](06-command-line.md)*
