# Solutions — 07 Section Quiz

Answer key for the quiz in [07 — Quiz and Checkpoints](../07-quiz-and-checkpoints.md).

---

## Part 1: Multiple Choice — Answer Key

| # | Answer | Explanation |
|---|--------|-------------|
| 1 | **B — Ada Lovelace** | Lovelace wrote an algorithm for Babbage's Analytical Engine in 1843, widely considered the first computer program. Turing defined computation theory; Babbage designed the machine; Hopper wrote the first compiler. |
| 2 | **B — Stored both program instructions and data in the same memory** | Before von Neumann, computers were often hardwired for specific tasks. Storing instructions and data together in the same memory made general-purpose computing practical. |
| 3 | **C — Storage functions as both input and output** | Saving a file = output to storage. Opening a file = input from storage. Storage is unique in this dual role. |
| 4 | **C — Performs mathematical calculations and logical comparisons** | The ALU handles all arithmetic (add, subtract, multiply, divide) and logic operations (comparisons, AND, OR, NOT). The Control Unit decodes instructions; cache stores frequently used data. |
| 5 | **B — When the CPU finds the needed data already stored in cache** | A cache hit means the data was in the fast nearby cache. A cache miss means the CPU must go to the slower RAM. High hit rates are essential for performance. |
| 6 | **C — A 32-bit address space can only represent 2³² unique memory addresses (4 GB)** | 2^32 = 4,294,967,296 bytes = exactly 4 GB. A 32-bit processor can only generate addresses in this range, so it physically cannot address more RAM than this. |
| 7 | **C — Manages hardware resources and provides services to applications** | The kernel is the core OS component that manages CPU scheduling, memory allocation, device access, and file systems. GUIs are provided by the shell/desktop environment, not the kernel. |
| 8 | **B — Using storage (SSD/HDD) as an extension of RAM when RAM is full** | Virtual memory is a technique where the OS swaps memory pages to disk when physical RAM is exhausted. It prevents crashes but is much slower than RAM. |
| 9 | **C — The majority of web servers, supercomputers, and Android devices** | ~96% of top web servers, 100% of the top 500 supercomputers, and all Android devices run Linux (or Linux-based kernels). It is the dominant OS for infrastructure. |
| 10 | **C — Searches for lines matching a pattern in a file** | `grep` (Global Regular Expression Print) prints every line that matches a given pattern. It doesn't list directories, delete files, or copy files. |
| 11 | **B — The repeating process by which a CPU retrieves, interprets, and runs instructions** | The Fetch-Decode-Execute cycle is the fundamental operation of every CPU: fetch the next instruction from memory, decode what it means, execute it, then repeat billions of times per second. |
| 12 | **C — Registers** | Registers are inside the CPU itself, are accessed in sub-nanosecond time, and hold data the CPU is actively working with. L3 Cache is fast but external to the core; RAM is on the motherboard; SSDs are storage. |
| 13 | **B — It made Unix portable — it could run on different hardware architectures** | Previously, OS code was written in assembly language for specific hardware. Writing Unix in C meant it could be recompiled for different CPUs. This portability made Unix the foundation for decades of OS development. |
| 14 | **C — Recursively deletes a folder and all its contents permanently** | `rm -r` removes directories recursively. The `-r` flag means "recursive" — it deletes the directory and everything inside it. There is no Recycle Bin; this is immediate and permanent. |
| 15 | **B — The observation that transistor count on chips roughly doubles every two years** | Gordon Moore (Intel co-founder) observed this trend in 1965. It drove the exponential improvement in computing performance for decades. It is now slowing as transistor sizes approach physical limits. |

---

## Part 2: Short Answer — Suggested Responses

### 1. Explain the memory hierarchy. Why do computers need multiple types of memory?

There is a fundamental trade-off in memory: fast memory is expensive and small, while large memory is slow and cheap. No single type of memory can be fast, large, *and* cheap at the same time.

Computers solve this by layering memory types: **registers** (inside the CPU, sub-nanosecond, bytes in size) → **L1/L2/L3 cache** (nanoseconds, megabytes) → **RAM** (60–100ns, gigabytes) → **SSD/HDD** (milliseconds, terabytes).

The CPU uses each layer to minimize waiting time. Cache keeps recently used data nearby so the CPU rarely has to go all the way to RAM. RAM keeps active programs loaded so the CPU rarely has to go to the SSD.

---

### 2. What is the difference between batch processing and time-sharing? Why was time-sharing significant?

**Batch processing** collects jobs (on punch cards) and runs them together without any interaction. Users submit work and come back later for results.

**Time-sharing** lets multiple users interact with the computer simultaneously. The OS rapidly switches the CPU between users, creating the illusion that each user has dedicated access.

Time-sharing was significant because it enabled **interactive computing** — users could see results immediately, make mistakes and correct them in real time, and use computers for exploration and development, not just pre-planned batch jobs. It set the foundation for terminals, then personal computers, then GUIs.

---

### 3. What is the difference between a CPU and a GPU? Why are GPUs used for machine learning?

A **CPU** has a small number of powerful, general-purpose cores (4–32 typically) designed to handle diverse, sequential tasks quickly.

A **GPU** has thousands of smaller, simpler cores designed to execute the same operation on many pieces of data simultaneously (parallel processing).

Machine learning — especially training neural networks — requires doing the same mathematical operation (matrix multiplication) on massive arrays of numbers. This is exactly the kind of problem GPUs excel at: thousands of cores each handling a small piece of the computation in parallel. A task that would take a CPU hours can take a GPU minutes.

---

### 4. A friend says "I have 16 GB of RAM but my computer is still slow." What are two possible explanations?

**Explanation 1 — CPU bottleneck:** The CPU may be old, slow, or throttled. RAM doesn't help if the CPU can't process data fast enough. A slow single-core task (like loading a complex web page) won't improve with more RAM.

**Explanation 2 — Storage bottleneck:** If the computer has a traditional HDD (mechanical hard drive), programs will take a long time to load and the OS will swap data to disk slowly. Upgrading from an HDD to an SSD often has a bigger impact on perceived speed than adding RAM.

Other valid answers: Too many background processes consuming CPU; thermal throttling (CPU slowing down to prevent overheating); malware; fragmented HDD; overloaded GPU during gaming.

---

### 5. What happens when you open a browser and navigate to a website?

**Hardware and OS layer view:**

1. **Input:** You double-click the browser icon (mouse sends click signal to CPU via OS)
2. **OS loads browser:** OS finds browser executable on SSD → loads it from storage into RAM
3. **CPU executes browser:** Fetch-Decode-Execute cycle begins; browser code runs in RAM
4. **Input:** You type a URL (keyboard input → OS → browser application)
5. **Browser renders UI:** GPU draws the browser window and address bar on screen (output)
6. **Network I/O:** Browser instructs the OS to open a network connection; the OS uses the network device driver to send TCP/IP packets over the network card
7. **Server responds:** Server returns HTML, CSS, and JavaScript files over the network
8. **CPU processes:** Browser's HTML parser (code running on CPU) builds the DOM from the received bytes; CSS is applied; JavaScript is executed
9. **GPU renders output:** The GPU converts the DOM into pixel data and sends it to the monitor
10. **Output:** You see the rendered web page

---

*Back to quiz: [07 — Quiz and Checkpoints](../07-quiz-and-checkpoints.md)*
