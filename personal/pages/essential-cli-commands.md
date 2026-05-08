# Essential CLI Commands Reference

**Command-Line Interface (CLI)** commands are fundamental for interacting with operating systems efficiently. Below is a structured guide to essential CLI commands, formatted for Joplin.

* * *

## Navigation Commands

These commands help you move through the file system and check your location:

- `cd` - **Change directory** (e.g., `cd Documents` moves to the "Documents" folder)
    - `cd /` - Navigate to the **system's root directory** (uses an absolute path)
    - `cd ~` - Go to the **current user's home directory**
    - `cd -` - Switch to the **previous directory** you were working in
- `pwd` - **Print Working Directory** (displays your exact location in the file system)

* * *

## File & Directory Management

Create, organize, and delete files and folders with these commands:

- `touch [filename]` - **Create a new empty file** (e.g., `touch notes.md` creates a Markdown file)
- `mkdir [directory]` - **Make a new directory** (e.g., `mkdir Projects` creates a "Projects" folder)
    - `mkdir -p [path]` - Create **nested directories** (e.g., `mkdir -p Music/Jazz` creates both "Music" and "Jazz")
- `ls` - **List contents** of the current directory
    - `ls -l` - Show a **detailed list** (includes permissions, file size, and modification date)
    - `ls -a` - List **hidden files/folders** (those starting with a `.`, like `.bashrc`)
    - `ls [path]` - List contents of a specific directory (e.g., `ls Downloads`)
- `rm [item]` - **Remove a file or empty directory**
    - `rm --rf [item]` - **Force remove** non-empty directories and all contents (**use with extreme caution!**)
- `cp [source] [destination]` - **Copy files/directories** (e.g., `cp report.pdf ~/Backups`)
    - `cp -r [source] [destination]` - Copy directories **recursively** (including all contents)
- `mv [source] [destination]` - **Move or rename** files/directories (e.g., `mv oldname.txt newname.txt` renames a file)

* * *

## File Content Operations

View, create, or edit file contents with these tools:

- `cat [filename]` - **Concatenate and display** file contents (e.g., `cat todo.txt` shows your to-do list)
    - `cat > [filename]` - **Write text to a file** (overwrites existing content; press `ctrl + d` to save)
    - `cat >> [filename]` - **Append text to a file** (preserves existing content)
- `echo "[text]"` - **Print text** to the terminal
    - `echo "[text]" > [filename]` - Write text directly to a file (overwrites)
    - `echo "[text]" >> [filename]` - Append text to a file (adds to the end)
- `nano [filename]` / `vim [filename]` - Open a **text editor** to modify files (`nano` is beginner-friendly)

* * *

## Output Redirection

Control where command output is sent (instead of just the terminal):

- `>` - **Redirect output to a file** (overwrites existing content)  
    **Example:** `ls -l > files_list.txt` saves the detailed directory list to "files_list.txt"
- `>>` - **Append output to a file** (adds to existing content instead of overwriting)  
    **Example:** `date >> activity_log.txt` adds the current date/time to "activity_log.txt"

* * *

## Terminal Controls

Manage your terminal session with these shortcuts:

- `clear` or `ctrl + l` - **Clear the terminal screen**
- `ctrl + c` - **Cancel the currently running command**
- `ctrl + d` - **Exit the terminal** or current mode (e.g., when editing with `cat >`)
- `exit` - **Close the terminal session** entirely

* * *

## System Information

Get details about your system, user, and commands:

- `date` - Show the **current date and time**
- `whoami` - Display the **username** of the currently logged-in user
- `[command] --help` - Show all **options/flags** for a command (e.g., `ls --help`)
- `man [command]` - Open the **manual page** for a command (more detailed than `--help`; press `q` to exit)
- `df -h` - Show **disk space usage** (in human-readable format, e.g., GB)
- `free -h` - Display **system memory (RAM) usage**

* * *

## Search & Filter

Find files or text efficiently:

- `find [path] -iname "[pattern]"` - **Search for files/directories by name**  
    **Example:** `find ~/Books -iname "*.pdf"` finds all PDF files in your "Books" folder
- `grep "[text]" [filename]` - **Search for text within a file**  
    **Example:** `grep "urgent" tasks.txt` finds all lines with "urgent" in "tasks.txt"
- `[command] | grep "[text]"` - **Filter command output** to show only lines with specific text  
    **Example:** `ls --help | grep "hidden"` shows help info related to hidden files

* * *

## Advanced Operations

For complex tasks, use these commands:

- `touch file{001..100}.txt` - **Create multiple files** (e.g., 100 files named "file001.txt" to "file100.txt")
- `chmod [permissions] [file]` - **Change file permissions** (e.g., `chmod 755 script.sh` makes a script executable)
- `sudo [command]` - Run a command with **administrative (root) privileges** (e.g., `sudo apt update` on Linux)
- `tar -czf [archive].tar.gz [files]` - **Create a compressed archive** (e.g., `tar -czf photos.tar.gz ~/Pictures`)
- `tar -xzf [archive].tar.gz` - **Extract a compressed archive**