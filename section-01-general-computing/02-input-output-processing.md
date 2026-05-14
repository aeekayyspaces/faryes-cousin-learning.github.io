# 02 — Input, Output, and Processing

## Learning Objectives

By the end of this page, you will be able to:

- Explain the Input → Process → Output model
- Identify common input and output devices and what they do
- Understand the role of the CPU as the "process" step
- Recognize that storage is both an input and an output
- Explain what a GPU is and why it's different from a CPU

---

## The Core Model: Input → Process → Output

Every computer operation, no matter how complex, follows the same fundamental pattern:

```
┌─────────┐     ┌──────────────┐     ┌─────────┐
│  INPUT  │────▶│   PROCESS    │────▶│ OUTPUT  │
│         │     │    (CPU)     │     │         │
└─────────┘     └──────────────┘     └─────────┘
                       ▲
                       │
                  ┌────┴────┐
                  │ STORAGE │
                  │ (Memory)│
                  └─────────┘
```

- **Input** — data or commands enter the computer
- **Process** — the CPU executes instructions on that data
- **Output** — results are delivered to the user or stored somewhere
- **Storage** — holds data and instructions before, during, and after processing

> **Analogy:** Think of a recipe and a chef. The raw ingredients are **input**. The chef following the recipe is **processing**. The finished dish is **output**. The written recipe stored in a book is **storage**.

This model applies to everything:
- Typing a document: keyboard input → word processor processes → text displays on screen (output)
- Watching YouTube: clicking play (input) → server streams video, your CPU decodes it (process) → video plays on screen (output)
- Taking a photo: pressing the shutter (input) → camera sensor and processor capture the image (process) → photo saved to storage (output)

---

## Input Devices

An **input device** is any hardware that sends data or commands into a computer.

### Common Input Devices

| Device | What It Inputs | How It Works |
|--------|---------------|--------------|
| **Keyboard** | Text and commands | Each key press sends a unique electrical signal (a "key code") to the CPU |
| **Mouse** | Pointer position and clicks | Optical sensor detects movement; buttons send click signals |
| **Touchscreen** | Touch position and gestures | Grid of capacitive sensors detects where and how your finger touches |
| **Microphone** | Audio | Converts sound waves (air pressure changes) into electrical signals |
| **Camera / Webcam** | Images and video | Sensor converts light into millions of tiny electrical values (pixels) |
| **Scanner** | Images and documents | Light source illuminates the document; sensor captures reflected light |
| **Game Controller** | Button presses and joystick position | Each button and axis sends its state continuously |
| **Sensors** | Temperature, location, motion | Accelerometers, GPS chips, thermometers — used heavily in smartphones |
| **Stylus / Pen tablet** | Precise drawing input | Pressure-sensitive surface tracks pen tip position and pressure |

### Input in Everyday Life

When you type a password:
1. Each key press sends an **electrical signal**
2. The keyboard converts it to a **key code**
3. The operating system **intercepts** the key code
4. The application **receives** the character and decides what to do with it (show a dot ● instead of the character, for security)

That entire sequence happens in **milliseconds**, invisibly.

---

## Output Devices

An **output device** receives processed data from the computer and presents it to the user in a usable form.

### Common Output Devices

| Device | What It Outputs | How It Works |
|--------|----------------|--------------|
| **Monitor / Screen** | Visual information | Millions of tiny light-emitting pixels, controlled individually to form images |
| **Printer** | Physical documents | Inkjet: sprays tiny ink droplets. Laser: uses heat and toner powder |
| **Speakers / Headphones** | Sound | Electrical signals vibrate a cone/driver, creating sound waves |
| **Haptic motor** | Vibration feedback | A small vibrating motor (in your phone) simulates physical sensations |
| **Projector** | Large-scale visual output | Projects an image from the GPU onto a surface |
| **LED indicators** | Status signals | Simple on/off or color indicators on devices |

### How Your Screen Works

A typical screen has millions of **pixels** (picture elements). Each pixel contains three sub-pixels: **Red**, **Green**, and **Blue** (RGB). By adjusting the brightness of each sub-pixel from 0 to 255, the screen can produce over 16 million colors.

When you watch a video at 60fps (frames per second), your screen is receiving and displaying **60 complete images every second** — and your brain perceives this as smooth motion.

> **Analogy:** A screen is like a mosaic made of millions of tiny colored tiles. From close up, you see individual dots. Step back and your brain assembles them into a coherent image.

---

## The Process: What the CPU Does

The **Central Processing Unit (CPU)** is the component that actually *processes* data. It executes instructions — billions of them per second — to transform input into output.

When you press "calculate" in a spreadsheet:
1. The button press is **input**
2. The CPU reads the formula and the numbers from memory
3. The CPU performs the arithmetic (addition, multiplication, etc.)
4. The result is written back to memory
5. The memory value is displayed on screen — **output**

We'll cover the CPU in much more detail in [03 — The CPU](03-the-cpu.md).

---

## Storage: Both Input and Output

**Storage** is unique because it functions as *both* input and output:

- When you **save a file**, data flows from the CPU *to* storage — storage acts as output
- When you **open a file**, data flows from storage *to* the CPU — storage acts as input

### Types of Storage

| Type | Speed | Capacity | Persistent? |
|------|-------|----------|-------------|
| **RAM** (Random Access Memory) | Very fast | Small (8–64 GB typical) | No — erased when power is off |
| **SSD** (Solid State Drive) | Fast | Medium (256 GB – 4 TB typical) | Yes |
| **HDD** (Hard Disk Drive) | Slower | Large (1 TB – 20 TB+) | Yes |
| **USB Flash Drive** | Medium | Small–Medium | Yes |
| **Cloud Storage** | Network-limited | Effectively unlimited | Yes |

> **Analogy:** RAM is like the papers on your desk right now — fast to access, but when you go home (power off), everything is cleared. An SSD is like a filing cabinet — takes a moment to open, but the files are there when you return tomorrow.

### What Happens When You Start Your Computer

1. Power on — only the **BIOS/firmware** is active (tiny, permanent storage on the motherboard)
2. BIOS loads the **bootloader** from your SSD/HDD
3. Bootloader loads the **operating system** from storage into RAM
4. The OS loads your **desktop** and starts services
5. When you open an app, it loads from storage into RAM
6. The CPU reads instructions from RAM and runs the app

Everything needs to pass through RAM before the CPU can use it. Storage is the long-term home; RAM is the working space.

---

## The GPU: A Specialized Processor

The **Graphics Processing Unit (GPU)** is a specialized processor designed for a specific kind of output: graphics.

### Why CPUs Aren't Enough for Graphics

A CPU is designed to handle a wide variety of tasks **one (or a few) at a time** — it has a small number of powerful cores.

Rendering graphics requires doing the **same simple calculation millions of times in parallel** — for example, calculating the color and brightness of every pixel on screen.

A modern GPU has **thousands of smaller cores** designed specifically for this kind of parallel work.

| | CPU | GPU |
|---|-----|-----|
| Cores | 8–64 powerful cores | 3,000–16,000+ smaller cores |
| Good at | Sequential, complex tasks | Parallel, repetitive tasks |
| Used for | Running your OS, apps, logic | Graphics, video, AI, scientific computation |

### GPUs Beyond Graphics

Today, GPUs are used far beyond displaying images:
- **Machine learning and AI** — training neural networks requires massive parallel computation
- **Video encoding/decoding** — converting video formats
- **Scientific simulation** — weather modeling, physics simulations
- **Cryptocurrency mining** — GPUs excel at the repetitive hashing calculations involved

> **Key Point:** When people talk about AI and machine learning requiring expensive hardware, they're largely talking about GPUs. The same chip that renders your video games is the engine behind AI models.

**Further Reading:**
- [How a GPU Works — Computerphile (YouTube)](https://www.youtube.com/watch?v=6stDhEA0wFQ)
- [GPU vs CPU Explained — Nvidia](https://www.nvidia.com/en-us/drivers/what-is-gpu/)

---

## Putting It All Together

Here's what happens when you open a photo editing app and apply a filter to an image:

```
1. MOUSE CLICK        → Input device sends signal
2. OS RECEIVES CLICK  → Input processed by operating system
3. APP OPENS FILTER   → CPU loads filter code from storage into RAM
4. IMAGE LOADED       → CPU loads image data from storage into RAM
5. GPU PROCESSES      → GPU applies filter math to every pixel (parallel)
6. RESULT IN RAM      → Processed image pixels held in memory
7. SCREEN UPDATES     → GPU sends pixel data to monitor
8. YOU SEE THE FILTER → Output on display
9. SAVE FILE          → CPU writes processed image back to storage (output to storage)
```

Every step maps back to our model: **Input → Process → Output**, with **Storage** supporting the whole flow.

---

## Check Your Understanding

1. Name three input devices and describe what type of data each one sends to the computer.
2. Why is storage considered both an input *and* an output device?
3. What is the difference between RAM and an SSD in terms of speed and persistence?
4. Why does a GPU use thousands of small cores instead of a few powerful ones?
5. What happens to data in RAM when you turn your computer off?

---

*Previous: [01 — History of Computers](01-history-of-computers.md)*
*Next: [03 — The CPU](03-the-cpu.md)*
