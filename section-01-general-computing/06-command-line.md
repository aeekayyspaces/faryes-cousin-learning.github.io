# 06 — The Command Line

## Learning Objectives

By the end of this page, you will be able to:

- Open a terminal on Windows, macOS, and Linux
- Navigate your file system using the command line
- Create, move, copy, and delete files and directories
- View file contents and system information
- Use piping and redirection to combine commands
- Understand the equivalent commands between Windows (PowerShell/CMD) and macOS/Linux (bash/zsh)

---

## What Is the Command Line?

The **command line** (also called the **terminal**, **shell**, or **console**) is a text-based interface to your operating system. Instead of clicking icons, you type commands and the OS responds with text.

> **Analogy:** The GUI (desktop with icons) is like a car with automatic transmission — easier to use, good for most situations. The command line is like a car with manual transmission — more effort to learn, but it gives you direct control and can be faster in skilled hands. Developers use the command line constantly because many tasks are faster, more scriptable, and more powerful than their GUI equivalents.

### Why Developers Use the Command Line

- **Speed:** Once you know commands, they're faster than navigating menus
- **Automation:** You can write scripts to repeat tasks automatically
- **Remote access:** Servers rarely have a GUI; you connect and control them via the command line (SSH)
- **Precision:** Every option is explicit — no hidden settings
- **Universal:** The same bash commands work on your Mac, a Linux server in the cloud, and a Raspberry Pi

---

## Opening a Terminal

### On macOS

1. Press `Cmd + Space` to open Spotlight
2. Type **Terminal** and press Enter

Or: **Applications → Utilities → Terminal**

macOS uses **zsh** by default (since macOS Catalina, 2019). You'll see a prompt like:
```
username@MacBook-Pro ~ %
```

### On Linux (Ubuntu)

Press `Ctrl + Alt + T` — this opens the terminal directly.

Or: Find **Terminal** in your applications menu.

Linux uses **bash** by default. Your prompt looks like:
```
username@hostname:~$
```

### On Windows: Command Prompt

Press `Win + R`, type `cmd`, press Enter.

You'll see a prompt like:
```
C:\Users\YourName>
```

`cmd.exe` is the classic Windows command line. It works but is limited. For most development work, use PowerShell instead.

### On Windows: PowerShell

Press `Win + X` and select **Windows PowerShell** (or **Terminal** on Windows 11).

PowerShell is far more powerful than cmd. It understands many Unix-style commands *and* adds its own powerful scripting capabilities.

```
PS C:\Users\YourName>
```

> **Tip:** On Windows 11, the **Windows Terminal** app unifies cmd, PowerShell, and WSL (Windows Subsystem for Linux) in one tabbed window. It's worth installing from the Microsoft Store.

---

## Understanding the Prompt

The prompt is the text displayed before your cursor. It tells you:

**On macOS/Linux:**
```
username@hostname:current-directory $
```
- `username` — who you're logged in as
- `hostname` — the computer's name
- `current-directory` — where in the file system you are (`~` means your home folder)
- `$` — indicates you're a regular user (`#` means root/administrator)

**On Windows (PowerShell):**
```
PS C:\Users\YourName>
```
- `PS` — PowerShell indicator
- `C:\Users\YourName` — current directory

---

## The File System

Before running commands, you need to understand how the file system is structured.

### macOS / Linux: A Tree from Root

Everything on a Unix-based system lives under a single root directory: `/`

```
/                     ← root
├── home/
│   └── yourname/     ← your home directory (~)
│       ├── Documents/
│       ├── Downloads/
│       └── Desktop/
├── usr/              ← user programs
├── etc/              ← system configuration files
├── bin/              ← essential system commands
├── tmp/              ← temporary files
└── var/              ← variable data (logs, etc.)
```

### Windows: Drive Letters

Windows organizes storage under drive letters:

```
C:\                   ← primary drive (usually)
├── Users\
│   └── YourName\     ← your home directory
│       ├── Documents\
│       ├── Downloads\
│       └── Desktop\
├── Program Files\    ← installed applications
└── Windows\          ← OS files

D:\                   ← secondary drive or DVD
```

---

## Navigation Commands

### Where Am I? (`pwd` / `cd`)

**macOS/Linux — print working directory:**
```bash
pwd
```
Output: `/home/yourname/Documents`

**Windows CMD:**
```cmd
cd
```
(With no arguments, prints current directory)

**Windows PowerShell:**
```powershell
Get-Location
# or the alias:
pwd
```

---

### What's in This Folder? (`ls` / `dir`)

**macOS/Linux:**
```bash
ls              # list files in current directory
ls -l           # long format: permissions, size, date
ls -a           # show hidden files (names starting with .)
ls -la          # long format + hidden files
ls /Documents   # list a specific path
```

Example output of `ls -l`:
```
drwxr-xr-x  5 user group  160 Jan 15 10:23 Documents
-rw-r--r--  1 user group 2048 Jan 14 09:10 notes.txt
```
Columns: permissions, links, owner, group, size, date, name

**Windows CMD:**
```cmd
dir             # list files
dir /a          # include hidden files
dir /s          # list recursively (all subdirectories)
```

**Windows PowerShell:**
```powershell
ls              # alias for Get-ChildItem
dir             # same alias
Get-ChildItem   # full command
Get-ChildItem -Hidden   # include hidden files
```

---

### Moving Around (`cd`)

**macOS/Linux/Windows — change directory:**
```bash
cd Documents            # go into Documents folder
cd ..                   # go up one level (to parent folder)
cd ../..                # go up two levels
cd ~                    # go to home directory (macOS/Linux)
cd /                    # go to root (macOS/Linux)
cd C:\Users\YourName    # go to specific Windows path
cd "My Folder"          # quote paths with spaces
```

> **Tip:** Press **Tab** to autocomplete a directory name. Start typing `cd Doc` and press Tab — the shell completes `Documents` for you. Press Tab twice to see all matches if there are multiple options.

---

## File Management Commands

### Creating Files and Directories

**macOS/Linux — create an empty file:**
```bash
touch newfile.txt       # create empty file (or update timestamp)
```

**macOS/Linux — create a directory:**
```bash
mkdir myfolder                      # create a folder
mkdir -p parent/child/grandchild    # create nested folders at once
```

**Windows CMD/PowerShell — create a directory:**
```cmd
mkdir myfolder
md myfolder             # same thing
```

**Windows PowerShell — create a file:**
```powershell
New-Item newfile.txt -ItemType File
```

---

### Copying Files and Directories (`cp` / `copy` / `xcopy`)

**macOS/Linux:**
```bash
cp file.txt backup.txt                  # copy file to new name
cp file.txt Documents/                  # copy file to a directory
cp -r myfolder/ backup-folder/          # copy entire directory (-r = recursive)
```

**Windows CMD:**
```cmd
copy file.txt backup.txt                # copy a file
xcopy myfolder backup-folder /E /I     # copy directory (/E = subdirs, /I = create dest)
```

**Windows PowerShell:**
```powershell
Copy-Item file.txt backup.txt
Copy-Item myfolder backup-folder -Recurse
```

---

### Moving and Renaming (`mv` / `move`)

Moving and renaming are the same operation — you're just changing the path of a file.

**macOS/Linux:**
```bash
mv oldname.txt newname.txt          # rename a file
mv file.txt Documents/              # move to a different directory
mv file.txt Documents/newname.txt   # move AND rename
mv folder/ Documents/               # move an entire folder
```

**Windows CMD:**
```cmd
ren oldname.txt newname.txt         # rename
move file.txt Documents\            # move
```

**Windows PowerShell:**
```powershell
Rename-Item oldname.txt newname.txt
Move-Item file.txt Documents\
```

---

### Deleting Files and Directories (`rm` / `del` / `rmdir`)

> **Warning:** There is no Recycle Bin in the terminal. Deleted files are **gone immediately**. Be careful.

**macOS/Linux:**
```bash
rm file.txt             # delete a file
rm -r myfolder/         # delete a directory and all its contents
rm -i file.txt          # interactive — asks for confirmation
```

**Windows CMD:**
```cmd
del file.txt            # delete a file
rmdir /s /q myfolder    # delete directory and contents (/s = subdirs, /q = quiet)
```

**Windows PowerShell:**
```powershell
Remove-Item file.txt
Remove-Item myfolder -Recurse
```

---

## Viewing File Contents

### Print File to Screen (`cat`)

**macOS/Linux:**
```bash
cat file.txt            # print entire file to screen
```

**Windows CMD:**
```cmd
type file.txt
```

**Windows PowerShell:**
```powershell
Get-Content file.txt
cat file.txt            # alias
```

---

### View Large Files Page by Page (`less` / `more`)

**macOS/Linux:**
```bash
less largefile.txt      # scroll through file (press q to quit)
more largefile.txt      # older pager (less is better)
```

**Windows CMD/PowerShell:**
```cmd
more largefile.txt
```

`less` controls: `Space` = next page, `b` = back, `/searchterm` = search, `q` = quit

---

### First and Last Lines (`head` / `tail`)

**macOS/Linux:**
```bash
head -n 20 file.txt     # first 20 lines
tail -n 20 file.txt     # last 20 lines
tail -f logfile.txt     # follow a file as it grows (useful for logs!)
```

---

## System Information Commands

### Disk Usage (`df` / `du`)

**macOS/Linux:**
```bash
df -h           # disk space on all mounted drives (-h = human-readable)
du -sh folder/  # size of a specific directory (-s = summary, -h = human-readable)
```

**Windows CMD:**
```cmd
wmic logicaldisk get size,freespace,caption
```

**Windows PowerShell:**
```powershell
Get-PSDrive C   # shows free and used space on C drive
```

---

### Running Processes (`top` / `htop` / Task Manager)

**macOS/Linux — top:**
```bash
top             # real-time view of running processes and resource usage
```

`top` shows: CPU%, memory%, PID (process ID), command name

Controls: `q` = quit, `k` = kill a process (enter PID), `h` = help

**macOS/Linux — htop (better version, may need to install):**
```bash
htop            # color, mouse support, much nicer interface
```

**Windows:**
```powershell
# View running processes:
Get-Process

# Sort by CPU usage:
Get-Process | Sort-Object CPU -Descending

# Or just open Task Manager:
taskmgr         # opens graphical Task Manager
```

---

### Network Info

**macOS/Linux:**
```bash
ifconfig        # network interfaces and IP addresses (older)
ip addr         # modern replacement for ifconfig (Linux)
ping google.com # test network connectivity
```

**Windows:**
```cmd
ipconfig        # IP address info
ping google.com # test connectivity
```

---

## Text Manipulation

### Search Inside Files (`grep`)

`grep` finds lines in a file that match a pattern — one of the most useful commands you'll use.

**macOS/Linux:**
```bash
grep "error" logfile.txt            # find lines containing "error"
grep -i "error" logfile.txt         # case-insensitive
grep -r "function" src/             # search recursively in all files in a directory
grep -n "TODO" *.js                 # show line numbers; search all .js files
grep -v "DEBUG" logfile.txt         # show lines NOT matching (invert)
```

**Windows PowerShell:**
```powershell
Select-String -Pattern "error" -Path logfile.txt
# or with the alias:
sls "error" logfile.txt
```

---

### Output Text (`echo`)

**macOS/Linux/Windows:**
```bash
echo "Hello, World!"            # print text to screen
echo $HOME                      # print a variable (macOS/Linux)
echo %USERPROFILE%              # print a variable (Windows CMD)
echo $env:USERPROFILE           # print a variable (PowerShell)
```

---

## Piping and Redirection

One of the most powerful features of the command line is combining commands.

### The Pipe (`|`)

The **pipe** operator sends the output of one command as the input to another.

```bash
ls -l | grep ".txt"         # list files, then filter for .txt files
cat access.log | grep "404" # print log, then show only 404 errors
ps aux | grep "chrome"      # list processes, then show only Chrome
```

> **Analogy:** A pipe is like a factory assembly line. The output of station 1 becomes the input of station 2. You can chain as many stations as you need.

### Output Redirection (`>` and `>>`)

**Redirect output to a file (overwrite):**
```bash
ls -l > filelist.txt        # save ls output to a file (overwrites if exists)
```

**Append output to a file:**
```bash
echo "new entry" >> log.txt # add line to end of file
```

**Redirect input from a file:**
```bash
sort < unsorted.txt         # sort reads from the file instead of keyboard
```

---

## Quick Reference Table

| Task | macOS/Linux | Windows CMD | Windows PowerShell |
|------|-------------|-------------|-------------------|
| Current directory | `pwd` | `cd` | `pwd` or `Get-Location` |
| List files | `ls` | `dir` | `ls` or `Get-ChildItem` |
| Change directory | `cd path` | `cd path` | `cd path` |
| Create directory | `mkdir dir` | `mkdir dir` | `mkdir dir` |
| Create file | `touch file` | `echo.>file` | `New-Item file` |
| Copy file | `cp src dst` | `copy src dst` | `Copy-Item src dst` |
| Move/rename | `mv src dst` | `move src dst` | `Move-Item src dst` |
| Delete file | `rm file` | `del file` | `Remove-Item file` |
| Delete directory | `rm -r dir` | `rmdir /s dir` | `Remove-Item dir -Recurse` |
| Print file | `cat file` | `type file` | `cat file` |
| Search in file | `grep text file` | `findstr text file` | `sls text file` |
| Running processes | `top` | `tasklist` | `Get-Process` |
| Clear screen | `clear` | `cls` | `clear` |
| Command history | `history` | `doskey /history` | `Get-History` |
| Get help | `man command` | `command /?` | `Get-Help command` |

---

## Exercises

Complete these exercises on your own machine. Answer keys are in the [solutions directory](solutions/06-command-line-solutions.md).

### Exercise 1: Navigation
1. Open a terminal.
2. Print your current directory.
3. Navigate to your home directory.
4. List all files, including hidden ones.
5. Navigate to your Desktop.

### Exercise 2: File Management
1. Create a new directory called `practice`.
2. Inside `practice`, create three empty files: `file1.txt`, `file2.txt`, `file3.txt`.
3. Copy `file1.txt` to `file1-backup.txt`.
4. Rename `file2.txt` to `notes.txt`.
5. Create a subdirectory called `archive` inside `practice`.
6. Move `file3.txt` into `archive/`.
7. List the contents of `practice` to confirm your work.

### Exercise 3: Viewing and Searching
1. Write a few lines to a file using `echo "line 1" > test.txt` and `echo "line 2" >> test.txt`.
2. Use the appropriate command to view the contents of `test.txt`.
3. Add the line `echo "error occurred" >> test.txt`.
4. Use `grep` (or `sls` on PowerShell) to search for the word "error" in `test.txt`.

### Exercise 4: System Information
1. Check how much free disk space you have.
2. View the list of currently running processes.
3. Ping `google.com` to test your network connection.

### Exercise 5: Pipes and Redirection
1. List all files in your home directory and redirect the output to a file called `homelist.txt`.
2. Use a pipe to list files and filter for any containing "doc" in the name.
3. Append the current date to `homelist.txt`.

---

## Check Your Understanding

1. What does `ls -la` do differently from just `ls`?
2. What is the difference between `>` and `>>` for output redirection?
3. If you delete a file using `rm` in the terminal, can you recover it from the Recycle Bin?
4. What does `tail -f logfile.txt` do, and when would it be useful?
5. How does a pipe (`|`) work? Give an example.

---

*Previous: [05 — Operating Systems](05-operating-systems.md)*
*Next: [07 — Quiz and Checkpoints](07-quiz-and-checkpoints.md)*
