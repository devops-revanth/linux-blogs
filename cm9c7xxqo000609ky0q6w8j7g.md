---
title: "Chapter 7 : Help Commands, Adding Text to Files, Redirection, I/O, tee, and Pipes in Linux"
seoTitle: "Linux Commands: Help, Text Editing, I/O, Pipes"
seoDescription: "Discover key Linux commands for help, text manipulation, I/O redirection, and workflows with tee and pipes in this guide"
datePublished: Fri Apr 11 2025 03:19:45 GMT+0000 (Coordinated Universal Time)
cuid: cm9c7xxqo000609ky0q6w8j7g
slug: help-commands-adding-text-to-files-redirection-io-tee-and-pipes-in-linux

---

## 🆘 Help Commands in Linux

In our previous blog, we discussed file permissions. But let’s be honest — sometimes we forget the syntax of a command, or even the command itself. Especially when you're prepping for certifications like **RHCSA** or **RHCE**, it’s normal to hit a mental block and wonder, *“What’s that command again?”*

Don’t worry — Linux has your back with several built-in ways to get help right from the terminal. Out of many, here are **six essential help commands** every Linux user should know:

### 1\. `whatis` command

* **Usage:** `whatis ls`
    
* **Description:** Gives you a **short summary** of what a command does.
    
* **Example Output:**
    
    ```bash
    ls (1) – list directory contents
    ```
    

### 2\. `--help` option

* **Usage:** `ls --help`
    
* **Description:** Most Linux commands support `--help` as a way to print **detailed usage instructions**, available options, and syntax examples.
    

### 3\. `man` command

* **Usage:** `man ls`
    
* **Description:** Shows the **manual page** of a command. This is the most common and powerful help command in Linux — think of it like a built-in instruction manual for every command.
    

### 4\. `which` command

* **Usage:** `which ls`
    
* **Description:** Displays the **full path** of the command that will run. Useful when you're trying to debug or locate binaries.
    

### 5\. `apropos` command

* **Usage:** `apropos copy`
    
* **Description:** Not sure what command to use but know a **keyword** like “copy”? `apropos` will search the man page database and list related commands.
    

### 6\. `type` command

* **Usage:** `type cd`
    
* **Description:** Tells you **what kind of command** you're dealing with — whether it’s a shell built-in, alias, function, or an actual binary.
    

## 📝 Adding Text to Files in Linux

In Linux, there are **many ways to create and add text to files** — and mastering these is absolutely essential. This is the **foundation** of working with files, scripts, configs, and more.

Here are the most common and useful methods:

---

### 1\. **Using** `echo` with redirection

#### 👉 Create or overwrite a file:

```bash
echo "Hello" > linux
```

* This will create a file named `linux` if it doesn’t exist.
    
* If the file already exists, it will **overwrite** the existing content.
    

#### 👉 Append to a file:

```bash
echo "How are you" >> linux
```

* This will **append** the new text to the existing content.
    
* It **won’t overwrite** the file.
    

---

### 2\. **Using** `cat` to create and write

```bash
cat > linux
```

* After running the command, type your text.
    
* To **save and exit**, press `Ctrl + D`.
    

> ⚠️ Be careful: this will **overwrite** the file content if it exists.

---

### 3\. **Using** `vi` (text editor)

```bash
vi linux
```

* Opens the file in `vi` editor.
    
* Press `i` to **enter insert mode**.
    
* Type your text.
    
* Press `Esc` → type `:wq!` → and hit `Enter` to **save and exit**.
    

> 🔖 We’ll explore `vi` in much more detail in a future blog. For now, just remember this basic flow.

## 🔄 Input/Output Redirection (I/O Redirection) in Linux

### 💡 What is I/O?

In Linux, every command interacts with 3 standard data streams:

1. **stdin (0)** – Standard Input  
    → Usually your **keyboard**.
    
2. **stdout (1)** – Standard Output  
    → The **result/output** shown on your **terminal**.
    
3. **stderr (2)** – Standard Error  
    → Any **error messages** that appear on the **screen**.
    

---

### 🔁 What is Redirection?

**Redirection** allows you to **change where input comes from** and **where output goes**.

📌 **By default**:

* Commands read input from `stdin` (keyboard)
    
* Show output via `stdout` (terminal)
    
* Send errors to `stderr` (terminal)
    

> With **redirection**, you can:

* Save output to a **file**
    
* Send **error messages** to another file
    
* Read input **from a file** instead of typing it manually
    

---

### 🚀 Common Redirection Operators (with Real Use):

#### 🔸 `>` → Redirect `stdout` (Overwrites file)

```bash
echo "Hello" > linux
```

* Creates `linux` file (if it doesn't exist)
    
* **Overwrites** the content if it does
    

---

#### 🔸 `>>` → Append to file (`stdout`)

```bash
echo "How are you" >> linux
```

* Appends to `linux` without removing old content
    

---

#### 🔸 `2>` → Redirect `stderr` (errors only)

```bash
ls wrong_file 2> error.txt
```

* Sends **error** to `error.txt` if file doesn't exist
    

---

#### 🔸 `2>>` → Append `stderr` to file

```bash
ls wrong_folder 2>> error.txt
```

* Adds more errors to the same file **without deleting** existing errors
    

---

#### 🔸 `&>` → Redirect both `stdout` & `stderr` (Bash only)

```bash
ls /etc /wrong_folder &> all_output.txt
```

* Sends both:
    
    * `stdout` (list of `/etc`)
        
    * `stderr` (error for `/wrong_folder`)  
        → to `all_output.txt`
        

> ✅ Verify with:

```bash
cat all_output.txt
```

---

### 🎯 Separate stdout and stderr to different files

```bash
ls /etc /wrong_folder >output.txt 2>error.txt
```

* `stdout` → `output.txt`
    
* `stderr` → `error.txt`
    

> ✅ Verify:

```bash
cat output.txt error.txt
```

---

### 🔁 `2>&1` → Send `stderr` to same place as `stdout`

```bash
ls /etc /wrong_folder 1> combined.txt 2>&1
```

* `stdout` → `combined.txt`
    
* `stderr` → redirected **to where stdout is going**
    

> ✅ Check:

```bash
cat combined.txt
```

---

### ⚠️ Wrong Version of Redirection

```bash
ls /etc /wrong_folder 2>&1 > wrong.txt
```

* Here, `stderr` is sent to **terminal** (stdout at that moment), then `stdout` goes to `wrong.txt`
    
* This order **won’t combine both outputs** as expected
    

## 🕳️ **Understanding** `/dev/null` – The Blackhole of Linux

### 📌 What is `/dev/null`?

`/dev/null` is a **special file** in Linux that **discards anything** written to it — like a **blackhole** for data.

* You can think of it as a **trash bin** where output is silently thrown away.
    
* Nothing comes out of it — anything sent to `/dev/null` is gone forever.
    
* **Used to suppress output**, especially **errors** or **verbose command output** you don’t want to see.
    

---

### ⚙️ **Real-World Use Cases of** `/dev/null`

---

### 1️⃣ Running Commands Silently (No Output at All)

📌 **Use Case**: You're running a **cronjob** or **automation script**, and you **don’t want logs or emails** filled with output.

```bash
some-command > /dev/null 2>&1
```

🔧 **Example**:

```bash
rsync -av /source /backup > /dev/null 2>&1
```

* `stdout` (normal output) → goes to `/dev/null`
    
* `stderr` (errors) → redirected to wherever `stdout` is going (`/dev/null`)
    
* ✅ Result: **No output at all**, quiet backup!
    

---

### 2️⃣ Logging Only Errors (Ignore Successful Output)

📌 **Use Case**: You're troubleshooting and only want to **capture errors**, not successful command output.

```bash
some-command > /dev/null 2> error.log
```

🔧 **Example**:

```bash
find / -name "*.conf" > /dev/null 2> error.log
```

* `stdout` → thrown away
    
* `stderr` → saved to `error.log`
    

✅ Result: **Only errors are logged**, not successful matches.

---

### 3️⃣ Capturing Only Output (Ignore Errors)

📌 **Use Case**: You want to save successful output but **ignore and suppress errors**.

```bash
some-command > output.txt 2> /dev/null
```

🔧 **Example**:

```bash
ls /etc/ /wrong_folder > files.txt 2> /dev/null
```

* `stdout` → saved in `files.txt`
    
* `stderr` → discarded silently
    

✅ Result: You get a clean file list, errors ignored!

---

### 4️⃣ Check if File Exists Without Showing Output

📌 **Use Case**: You want to **check file existence** without seeing any errors or output — just a **clean yes/no response**.

```bash
test -f somefile.txt > /dev/null 2>&1 && echo "Exists" || echo "No file"
```

Or:

```bash
[ -f somefile.txt ] && echo "Found" || echo "Not found"
```

✅ `test` command silently checks if file exists  
✅ `> /dev/null 2>&1` hides any errors

## 🌪️ `tee` Command – Split Your Output Like a Pro

### 📌 What is the `tee` Command?

`tee` is a powerful command that **reads from standard input (stdin)** and **writes to both standard output (your terminal)** and **a file** at the **same time**.

🔧 Think of it like a **T-junction** in plumbing – the data goes in, then splits into **two directions**: screen & file!

### 🛠️ Syntax:

```bash
command | tee [options] filename
```

---

### ✅ **Use Cases of** `tee` Command

---

### 1️⃣ View & Save Output Simultaneously

📌 **Use Case**: You're running a command and want to **see the output on-screen** while **saving it to a file** for logging or future reference.

```bash
echo "Hello" | tee hello.txt
```

📤 Output:

* Terminal: `Hello`
    
* File `hello.txt`: contains `Hello`
    

---

### 2️⃣ Append to a File (Instead of Overwriting)

📌 **Use Case**: You want to **keep old logs** and **add new lines** to the file without wiping it.

```bash
echo "Line1" | tee log.txt
echo "Line2" | tee -a log.txt
```

🗂️ `log.txt` content:

```bash
Line1
Line2
```

> Use `-a` to append instead of overwrite.

---

### 3️⃣ Combine with Other Commands (Pipeline Magic)

📌 **Use Case**: Log full output and **filter important lines live** on your screen.

```bash
df -h | tee disk.txt | grep /dev
```

🔍 What happens:

* Saves entire output of `df -h` to `disk.txt`
    
* Shows only `/dev` lines in terminal
    

---

### 4️⃣ Use with `sudo` to Edit Root-Owned Files

📌 **Use Case**: You want to write to a file that **needs root permission** – but `sudo > file` doesn’t work.

❌ Won’t work:

```bash
echo "something" > /etc/config.conf
```

✅ Correct way:

```bash
echo "new config" | sudo tee -a /etc/config.conf
```

* `sudo` works with `tee` to **gain permission**
    
* Append mode (`-a`) avoids overwriting existing config
    

---

### 5️⃣ Real Use Case: Log Script Output While Monitoring

📌 **Use Case**: You're running a backup script and want to **monitor progress** and **save logs**.

```bash
./backup.sh | tee backup_log.txt
```

* Watch it run in real time
    
* Later, review the full log inside `backup_log.txt`
    

---

### 🔁 Summary

| Action | Command |
| --- | --- |
| Save + show output | \`command |
| Append + show output | \`command |
| Save + filter | \`command |
| Write with sudo | `echo "text" | sudo tee file` |

---

## 🧩 **Pipes in Linux (**`|`) – Your Command Chain Connector

---

### 🔧 **What is a Pipe (**`|`) in Linux?

A **pipe** is a powerful feature in Linux that **passes the output of one command** as the **input to another**. Think of it like a **conveyor belt** 🛠️ — data flows from one tool to the next.

### 🛠️ Syntax:

```bash
command1 | command2
```

---

### ✅ **Use Cases of Pipes**

---

### 1️⃣ Filtering Output Easily

📌 **Use Case**: The command gives too much output — use `pipe` to filter only what you want.

```bash
ps aux | grep ssh
```

* `ps aux` → lists **all processes**
    
* `grep ssh` → filters processes that include **"ssh"**
    

---

### 2️⃣ Chaining Multiple Commands

📌 **Use Case**: You want to process data through multiple tools without creating temporary files.

```bash
cat file.txt | wc -l
```

* `cat file.txt` → shows file content
    
* `wc -l` → counts the number of lines
    

💡 **Better & Smarter Linux Way** (without `cat`):

```bash
wc -l < file.txt
```

Same result, more efficient — smart admins prefer this 🔥

---

### 3️⃣ Real-Time Log Monitoring

📌 **Use Case**: You're checking logs and want to see **only errors**, live as they happen.

```bash
tail -f /var/log/syslog | grep "error"
```

* `tail -f` → follows log file in real time
    
* `grep "error"` → shows only lines containing **"error"**
    

👀 Great for live debugging or production monitoring!

---

### 4️⃣ Simplify Scripts & Avoid Temp Files

📌 Combine commands directly:

```bash
cat /etc/passwd | cut -d: -f1 | sort | uniq
```

* Get all usernames
    
* Sort them
    
* Remove duplicates — all in **one line**
    

No need to store intermediate results. Super clean 💯

---

### 5️⃣ Combine with `tee` for Logging

📌 Want to **see output** on screen and **log it**?

```bash
df -h | tee disk_log.txt | grep /dev
```

* Saves entire output to `disk_log.txt`
    
* Shows filtered output (`/dev`) on terminal
    

---

### 🔁 Summary

| Use Case | Example |
| --- | --- |
| Filter command output | `ps aux | grep nginx` |
| Count lines in file | `cat file.txt | wc -l` |
| Monitor logs for error | `tail -f logfile | grep error` |
| Chain tools together | `cat file | cut | sort | uniq` |
| Save & view | `command | tee log.txt | grep something` |

### ✅ Wrapping Up

In this chapter, we explored some of the most useful tools for any Linux user:

* How to quickly find help using `whatis`, `--help`, `man`, `which`, `apropos`, and `type`
    
* Different ways to add text to files using `echo`, `cat`, and `vi`
    
* Input/Output Redirection to control where data and errors go
    
* The magic of `/dev/null` to silence output or clean logs
    
* The `tee` command to split output and save it while watching in real-time
    
* Using pipes `|` to chain commands and build powerful workflows
    

These commands might seem basic, but they’re the backbone of daily Linux usage. Mastering these is like learning the secret shortcuts of the Linux universe.

Thanks for reading!

📌 Blog: [blog.durgarevu.com](https://blog.durgarevu.com)  
📧 Reach me: revanth1226@gmail.com