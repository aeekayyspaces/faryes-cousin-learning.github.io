# 07 — Section Quiz and Checkpoints

## Section 1 Learning Objectives Review

Before taking this quiz, you should be able to:

- [ ] Describe the major eras in computing history and key figures
- [ ] Explain the Input → Process → Output model
- [ ] Identify common input and output devices
- [ ] Describe the Fetch-Decode-Execute cycle
- [ ] Explain the CPU's key components (ALU, CU, registers, cache)
- [ ] Describe the memory hierarchy from registers to cloud storage
- [ ] Explain what an operating system is and what the kernel does
- [ ] Navigate a file system and perform basic file operations in the terminal

If you can't confidently check off an item, review the relevant page before continuing.

---

## Part 1: Multiple Choice

Choose the best answer for each question. Answer key in [solutions/07-quiz-solutions.md](solutions/07-quiz-solutions.md).

---

**1.** Who wrote what is widely considered the first computer program?

- A) Alan Turing
- B) Ada Lovelace
- C) Charles Babbage
- D) Grace Hopper

---

**2.** The Von Neumann architecture is significant because it:

- A) Was the first computer to use vacuum tubes
- B) Stored both program instructions and data in the same memory
- C) Introduced the concept of a graphical user interface
- D) Was the first computer to use transistors instead of vacuum tubes

---

**3.** In the Input → Process → Output model, where does storage fit?

- A) Storage is only an input device
- B) Storage is only an output device
- C) Storage functions as both input and output
- D) Storage is separate from the I/O model entirely

---

**4.** What does the Arithmetic Logic Unit (ALU) do?

- A) Manages which process runs on the CPU
- B) Stores recently used instructions for fast access
- C) Performs mathematical calculations and logical comparisons
- D) Decodes instructions from binary to CPU signals

---

**5.** What is a cache hit?

- A) When the CPU needs to fetch data from RAM because it's not in cache
- B) When the CPU finds the needed data already stored in cache
- C) When a program crashes and the OS must clear the cache
- D) When two processes try to access the same cache entry simultaneously

---

**6.** A 32-bit processor cannot use more than 4 GB of RAM because:

- A) RAM chips larger than 4 GB didn't exist when 32-bit CPUs were designed
- B) The operating system reserves the extra RAM for the kernel
- C) A 32-bit address space can only represent 2³² unique memory addresses (4 GB)
- D) 32-bit processors don't support virtual memory

---

**7.** Which of the following best describes what an operating system kernel does?

- A) Provides a graphical user interface for users
- B) Compiles programs from source code to machine code
- C) Manages hardware resources and provides services to applications
- D) Stores the user's files and documents on disk

---

**8.** What is virtual memory?

- A) Memory that exists only inside CPU registers
- B) Using storage (SSD/HDD) as an extension of RAM when RAM is full
- C) The address space reserved for kernel operations
- D) Memory shared between multiple CPU cores

---

**9.** Linux is used on:

- A) Only desktop computers running Ubuntu
- B) Primarily embedded systems and IoT devices
- C) The majority of web servers, supercomputers, and Android devices
- D) Only scientific research computers

---

**10.** What does the `grep` command do?

- A) Lists files in a directory
- B) Deletes files matching a pattern
- C) Searches for lines matching a pattern in a file
- D) Copies files from one location to another

---

**11.** What is the Fetch-Decode-Execute cycle?

- A) The process by which an OS loads programs from disk into RAM
- B) The repeating process by which a CPU retrieves, interprets, and runs instructions
- C) The sequence of steps in a compiler translating source code to machine code
- D) The process of copying data between RAM and cache

---

**12.** Which memory type is the fastest and located directly inside the CPU?

- A) L3 Cache
- B) RAM
- C) Registers
- D) SSD (NVMe)

---

**13.** What was significant about Unix being written in the C programming language?

- A) It made Unix faster than all competing operating systems
- B) It made Unix portable — it could run on different hardware architectures
- C) It introduced object-oriented programming to operating system design
- D) It allowed Unix to support graphical user interfaces

---

**14.** In macOS/Linux, what does `rm -r foldername` do?

- A) Renames a folder
- B) Removes a folder and moves it to the Recycle Bin
- C) Recursively deletes a folder and all its contents permanently
- D) Creates a compressed archive of the folder

---

**15.** What is Moore's Law?

- A) The principle that software always expands to fill available hardware capacity
- B) The observation that transistor count on chips roughly doubles every two years
- C) The rule that CPU clock speed must be at least 2x RAM speed for optimal performance
- D) The law requiring operating systems to be backward compatible

---

## Part 2: Short Answer

Answer in 2–4 sentences each. Suggested answers in [solutions/07-quiz-solutions.md](solutions/07-quiz-solutions.md).

**1.** Explain the memory hierarchy. Why do computers need multiple types of memory rather than just one?

**2.** What is the difference between batch processing and time-sharing? Why was time-sharing a significant advance?

**3.** What is the difference between a CPU and a GPU? Why are GPUs used for machine learning?

**4.** A friend says "I have 16 GB of RAM but my computer is still slow." What are two possible explanations?

**5.** Explain what happens, step by step, when you open a browser and navigate to a website. Focus on the hardware and OS layers (Input → Process → Output, storage, CPU, etc.).

---

## Part 3: Code Challenges

These challenges require you to use the terminal. Solutions are in the [solutions directory](solutions/06-command-line-solutions.md).

---

### Challenge 1: File System Explorer

**Goal:** Practice navigation and listing commands.

1. Open a terminal.
2. Navigate to your home directory.
3. List all files including hidden ones, in long format.
4. Create a directory called `tutorial-workspace`.
5. Navigate into it.
6. Confirm your location by printing the current path.

**Expected final output:** Your current directory should be something like `/home/yourname/tutorial-workspace` or `C:\Users\YourName\tutorial-workspace`.

---

### Challenge 2: File Operations

**Goal:** Create, copy, move, and delete files.

Inside `tutorial-workspace`:

1. Create a file named `hello.txt`.
2. Write the text "Hello, command line!" into it (hint: use `echo`).
3. View the contents of `hello.txt`.
4. Copy `hello.txt` to `hello-backup.txt`.
5. Create a subdirectory named `backups`.
6. Move `hello-backup.txt` into `backups/`.
7. List the directory contents to verify everything is where it should be.
8. Print the contents of `backups/hello-backup.txt` without navigating into `backups/`.

---

### Challenge 3: System Detective

**Goal:** Use system commands to answer questions about your computer.

Find and record:
1. How much free disk space is available on your primary drive?
2. How many processes are currently running on your system?
3. What is your computer's local IP address?
4. What user are you logged in as? (Hint: `whoami` on macOS/Linux, `$env:USERNAME` on PowerShell)

---

### Challenge 4: Search and Filter

**Goal:** Use grep/pipes to process text.

1. Create a file called `log.txt` with the following content (add each line using `echo "..." >> log.txt`):
   ```
   INFO: Server started
   DEBUG: Connection attempt from 192.168.1.1
   ERROR: Database connection failed
   INFO: Retry attempt 1
   ERROR: Database connection failed
   INFO: Server shutting down
   ```
2. Search `log.txt` for lines containing "ERROR".
3. Count how many lines contain "ERROR" (hint: pipe grep to `wc -l` on macOS/Linux, or count manually).
4. Redirect all "INFO" lines to a new file called `info-only.txt`.
5. View `info-only.txt` to confirm.

---

## Completion Checklist

When you've finished this section, you should be able to answer "yes" to all of these:

- [ ] I can describe how computers evolved from ENIAC to smartphones
- [ ] I can explain what happens when the CPU executes a program (Fetch-Decode-Execute)
- [ ] I understand why the memory hierarchy exists and how RAM and cache relate
- [ ] I can explain what an OS does and why Linux is important for developers
- [ ] I can navigate directories, create and manage files, and view content in a terminal
- [ ] I can use pipes and redirection to combine commands
- [ ] I completed all four code challenges

---

*Ready for the next section? → [Section 2: Networking and Protocols](../section-02-networking/01-what-is-a-network.md)*

*Review sources: [08 — Sources](08-sources.md)*
