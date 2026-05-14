# Solutions — 06 Command Line Exercises

These are the reference solutions for the exercises and code challenges in [06 — The Command Line](../06-command-line.md) and [07 — Quiz and Checkpoints](../07-quiz-and-checkpoints.md).

---

## Exercise 1: Navigation

### Steps and Commands

**macOS / Linux:**
```bash
# Step 1: Open a terminal (Cmd+Space → "Terminal" on macOS, Ctrl+Alt+T on Ubuntu)

# Step 2: Print current directory
pwd
# Example output: /home/yourname

# Step 3: Navigate to home directory
cd ~
# or just: cd

# Step 4: List all files including hidden ones
ls -la
# The -l flag gives detailed format, -a shows hidden files (those starting with .)
# You'll see entries like .bashrc, .zshrc, .profile — these are config files

# Step 5: Navigate to Desktop
cd ~/Desktop
# or: cd Desktop  (if you're already in home)
```

**Windows PowerShell:**
```powershell
# Step 2: Print current directory
pwd
# or: Get-Location

# Step 3: Navigate to home directory
cd ~
# or: cd $HOME

# Step 4: List all files including hidden ones
ls -Force
# or: Get-ChildItem -Force

# Step 5: Navigate to Desktop
cd ~/Desktop
```

**What to notice:**
- Hidden files on macOS/Linux start with a `.` (dot). These are configuration files the OS doesn't normally display.
- `~` is a shortcut for your home directory. It expands to the full path automatically.

---

## Exercise 2: File Management

### Steps and Commands

**macOS / Linux:**
```bash
# Step 1: Create practice directory
mkdir practice

# Step 2: Create three empty files inside practice
cd practice
touch file1.txt file2.txt file3.txt
# You can touch multiple files at once by separating names with spaces

# Step 3: Copy file1.txt
cp file1.txt file1-backup.txt

# Step 4: Rename file2.txt to notes.txt
mv file2.txt notes.txt
# mv (move) with a new name in the same directory = rename

# Step 5: Create archive subdirectory
mkdir archive

# Step 6: Move file3.txt into archive
mv file3.txt archive/

# Step 7: List contents of practice
ls -l
# Expected output:
# -rw-r--r-- file1-backup.txt
# -rw-r--r-- file1.txt
# -rw-r--r-- notes.txt
# drwxr-xr-x archive/
```

**Windows PowerShell:**
```powershell
# Step 1
mkdir practice

# Step 2
cd practice
New-Item file1.txt, file2.txt, file3.txt -ItemType File

# Step 3
Copy-Item file1.txt file1-backup.txt

# Step 4
Rename-Item file2.txt notes.txt

# Step 5
mkdir archive

# Step 6
Move-Item file3.txt archive\

# Step 7
ls
```

**Common mistakes:**
- Forgetting the trailing `/` after `archive` in `mv file3.txt archive/` — the slash tells `mv` it's a directory, not a new filename. (Both work in practice, but the slash makes intent clear.)
- Using `ren` in PowerShell (it's a CMD alias) — it works, but `Rename-Item` is more explicit.

---

## Exercise 3: Viewing and Searching

**macOS / Linux:**
```bash
# Step 1: Write two lines to a file
echo "line 1" > test.txt       # > creates the file (or overwrites it)
echo "line 2" >> test.txt      # >> appends to it

# Step 2: View the file
cat test.txt
# Output:
# line 1
# line 2

# Step 3: Add an error line
echo "error occurred" >> test.txt

# Step 4: Search for "error"
grep "error" test.txt
# Output:
# error occurred
```

**Windows PowerShell:**
```powershell
# Step 1
echo "line 1" > test.txt
echo "line 2" >> test.txt

# Step 2
cat test.txt

# Step 3
echo "error occurred" >> test.txt

# Step 4
Select-String "error" test.txt
# or: sls "error" test.txt
```

**What to notice:**
- `>` and `>>` are different. Using `>` twice on the same file would erase the first line. Always use `>>` when you want to add to an existing file.
- `grep` is case-sensitive by default. `grep "Error" test.txt` would NOT match "error occurred". Use `grep -i` for case-insensitive matching.

---

## Exercise 4: System Information

**macOS / Linux:**
```bash
# Step 1: Disk space
df -h
# Look for the line with "/" (root) or your main drive
# "Avail" column shows free space

# Step 2: Running processes
top
# Press q to quit
# Or for a count: ps aux | wc -l

# Step 3: Ping
ping google.com
# Press Ctrl+C to stop
# Look for "time=XX ms" — that's your latency
# If you get "Request timeout," your internet may be down or ICMP is blocked
```

**Windows PowerShell:**
```powershell
# Step 1: Disk space
Get-PSDrive C
# Shows Used and Free space

# Step 2: Running processes
Get-Process
# Or open Task Manager: taskmgr

# Step 3: Ping
ping google.com
```

**What to notice:**
- `df` output uses `1K-blocks` by default. The `-h` flag converts to human-readable units (MB, GB).
- `ping` sends ICMP packets. Some networks and servers block them, so not all hosts are pingable even when reachable.

---

## Code Challenge Solutions

### Challenge 1: File System Explorer

**macOS / Linux:**
```bash
cd ~
ls -la
mkdir tutorial-workspace
cd tutorial-workspace
pwd
# Output should be: /home/yourname/tutorial-workspace (or /Users/yourname/tutorial-workspace on macOS)
```

**Windows PowerShell:**
```powershell
cd ~
ls -Force
mkdir tutorial-workspace
cd tutorial-workspace
pwd
# Output: C:\Users\YourName\tutorial-workspace
```

---

### Challenge 2: File Operations

**macOS / Linux:**
```bash
# Assume you're inside tutorial-workspace
touch hello.txt
echo "Hello, command line!" > hello.txt
cat hello.txt
cp hello.txt hello-backup.txt
mkdir backups
mv hello-backup.txt backups/
ls -l
# Expected:
# drwxr-xr-x  backups/
# -rw-r--r--  hello.txt

# Print backup file contents without navigating into backups/
cat backups/hello-backup.txt
# Output: Hello, command line!
```

**Windows PowerShell:**
```powershell
New-Item hello.txt -ItemType File
"Hello, command line!" > hello.txt
cat hello.txt
Copy-Item hello.txt hello-backup.txt
mkdir backups
Move-Item hello-backup.txt backups\
ls
cat backups\hello-backup.txt
```

**Key concept:** `cat backups/hello-backup.txt` demonstrates that you can reference files in subdirectories using a **relative path** — you don't have to `cd` into a directory to read its files.

---

### Challenge 3: System Detective

**macOS:**
```bash
# 1. Free disk space
df -h /
# Look for the "Avail" column on the "/" row

# 2. Number of running processes
ps aux | wc -l
# Subtract 1 for the header line

# 3. Local IP address
ifconfig | grep "inet " | grep -v 127.0.0.1
# Look for an address like 192.168.x.x or 10.x.x.x

# 4. Current user
whoami
```

**Linux:**
```bash
# 1. Free disk space
df -h

# 2. Number of running processes
ps aux | wc -l

# 3. Local IP address
ip addr show | grep "inet "
# or: hostname -I

# 4. Current user
whoami
```

**Windows PowerShell:**
```powershell
# 1. Free disk space
Get-PSDrive C | Select-Object Used, Free

# 2. Number of running processes
(Get-Process).Count

# 3. Local IP address
ipconfig
# Look for "IPv4 Address" under your active adapter

# 4. Current user
$env:USERNAME
# or: whoami
```

---

### Challenge 4: Search and Filter

**macOS / Linux:**
```bash
# Step 1: Create log.txt
echo "INFO: Server started" > log.txt
echo "DEBUG: Connection attempt from 192.168.1.1" >> log.txt
echo "ERROR: Database connection failed" >> log.txt
echo "INFO: Retry attempt 1" >> log.txt
echo "ERROR: Database connection failed" >> log.txt
echo "INFO: Server shutting down" >> log.txt

# Step 2: Search for ERROR lines
grep "ERROR" log.txt
# Output:
# ERROR: Database connection failed
# ERROR: Database connection failed

# Step 3: Count ERROR lines
grep "ERROR" log.txt | wc -l
# Output: 2

# Step 4: Redirect INFO lines to a new file
grep "INFO" log.txt > info-only.txt

# Step 5: View info-only.txt
cat info-only.txt
# Output:
# INFO: Server started
# INFO: Retry attempt 1
# INFO: Server shutting down
```

**Windows PowerShell:**
```powershell
# Step 1: Create log.txt
"INFO: Server started" > log.txt
"DEBUG: Connection attempt from 192.168.1.1" >> log.txt
"ERROR: Database connection failed" >> log.txt
"INFO: Retry attempt 1" >> log.txt
"ERROR: Database connection failed" >> log.txt
"INFO: Server shutting down" >> log.txt

# Step 2: Search for ERROR lines
sls "ERROR" log.txt

# Step 3: Count ERROR lines
(sls "ERROR" log.txt).Count

# Step 4: Redirect INFO lines
sls "INFO" log.txt | ForEach-Object { $_.Line } > info-only.txt

# Step 5: View file
cat info-only.txt
```

**What to notice:**
- `grep "ERROR" log.txt | wc -l` is a pipeline: grep outputs matching lines, and `wc -l` counts them.
- The `>` redirect creates or overwrites `info-only.txt`. If we had used `>>`, it would append to any existing file.
- This pattern (filter logs by level) is something you'll do constantly as a developer diagnosing production issues.

---

*Back to exercises: [06 — The Command Line](../06-command-line.md)*
