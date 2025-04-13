---
title: "Chapter 9 : Mastering File & Text Processing Commands in Linux"
seoTitle: "Linux File & Text Command Mastery"
seoDescription: "Master key Linux commands: `cut`, `awk`, `grep` for effective file and text data handling in scripts"
datePublished: Sun Apr 13 2025 03:06:46 GMT+0000 (Coordinated Universal Time)
cuid: cm9f2cxfs000u09l820ai84kz
slug: mastering-file-and-text-processing-commands-in-linux
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1744513577028/c70239bd-173f-49a9-b5b4-f09aab2be956.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1744513562523/2c42adc4-121e-4c63-9f28-0e74cbe5b3b3.png

---

Hey everyone 👋

So far in our Linux journey, we’ve laid down a solid foundation. We’ve covered everything from what Linux is, understanding the filesystem, permissions, user commands, file handling, and most recently—how to maintain and display files efficiently.

Now, we’re stepping into a powerful zone: **File & Text Processing**.

⚡ **Why is this important?**  
This chapter is not just another one. This is your **first real step into the scripting world**. Whether you aim to become a **Linux admin, DevOps engineer, or someone writing automation scripts**—these are the commands you’ll rely on daily to **filter, format, compare, and analyze** data from files, logs, or command outputs.

In today’s blog, we will cover some of the most powerful and commonly used file/text processing tools in Linux:

🔹 `cut` – Extract sections from each line of a file  
🔹 `awk` – A full-on text scanning and reporting language  
🔹 `grep` – Search text using patterns  
🔹 `sort` – Arrange lines in a file in a specific order  
🔹 `wc` – Count lines, words, characters  
🔹 `diff` & `cmp` – Compare files and spot differences  

💡 You may not write huge scripts yet, but after this chapter, **you’ll have all the tools** you need to manipulate text like a pro—whether you’re analyzing logs, building reports, or preparing data for automation tasks.

So buckle up 🚀  
Let’s begin mastering each one—**from** `cut` to cmp, one command at a time.

✅ **We'll use this** `employees.txt` file throughout **Chapter 9** to learn and practice each command

### 🗂 Sample File: `employees.txt` (with `:` delimiter)

```bash
ID:Name:Department:Role:Salary
101:Ravi:Engineering:Developer:75000
102:Anita:Marketing:Manager:68000
103:Suresh:Engineering:DevOps:72000
104:Priya:HR:Executive:50000
105:John:Engineering:Developer:76000
106:Neha:Marketing:Executive:54000
107:Amit:Finance:Analyst:60000
108:Kiran:Engineering:DevOps:71000
109:Divya:HR:Manager:65000
110:Sam:Engineering:Developer:77000
111:Leela:Finance:Manager:69000
112:Arjun:Support:Executive:48000
113:Manoj:Support:Manager:58000
114:Sneha:Engineering:Tester:53000
115:Raj:Marketing:Executive:56000
```

📌 **We will use above file with** `:` delimiter to learn how to:

* Extract specific fields using `cut`
    
* Filter entries using `grep` and `egrep`
    
* Perform complex field-wise operations using `awk`
    
* Sort and remove duplicates with `sort` and `uniq`
    
* Count and analyze data using `wc`
    
* Compare file versions using `diff` and `cmp`
    

🛠️ Let’s get into it — starting with the `cut` command to slice this file like a data ninja!

## ✂️ `cut` Command in Linux

The `cut` command is used to **extract specific sections from each line** of a file or input — like slicing columns or characters from structured data. It's **fast, lightweight**, and perfect for log processing, CSV/TSV parsing, or shell scripting.

### 📌 Syntax:

```bash
cut OPTION... [FILE]
```

### 🧩 Common Options:

| Option | Description |
| --- | --- |
| `-d` | Specifies the delimiter (default is TAB) |
| `-f` | Specifies the field number(s) to extract |
| `-c` | Cuts characters by position |
| `--complement` | Displays all fields **except** those specified |
| `--output-delimiter` | Change the output delimiter when extracting multiple fields |

### 📁 Let’s use our file: `employees.txt`

#### 🗂 Sample Line:

```bash
101:Ravi:Engineering:Developer:75000
```

### ✅ Examples

#### 1\. Extract only Names (2nd field)

```bash
cut -d ":" -f2 employees.txt
```

📤 **Output:**

```bash
Name
Ravi
Anita
Suresh
...
```

#### 2\. Get Names and Departments (Fields 2 & 3)

```bash
cut -d ":" -f2,3 employees.txt
```

📤 Output:

```bash
Name:Department
Ravi:Engineering
Anita:Marketing
...
```

#### 3\. Get everything *except* the Salary field

```bash
cut -d ":" --complement -f5 employees.txt
```

📤 Output:

```bash
ID:Name:Department:Role
101:Ravi:Engineering:Developer
...
```

#### 4\. Cut characters (first 5 characters from each line)

```bash
cut -c 1-5 employees.txt
```

📤 Output:

```bash
ID:Na
101:R
102:A
...
```

### ⚙️ Real-World Use Cases

✅ **Log Parsing**  
Extract IPs from NGINX logs:

```bash
cut -d ' ' -f1 /var/log/nginx/access.log
```

✅ **Parse CSV or Colon-separated files**  
Get employee names and salaries from a payroll report:

```bash
cut -d ":" -f2,5 employees.txt
```

✅ **Quick shell scripts**  
Combine with `grep`, `awk`, or `sort` for powerful pipelines.

### 💡 Pro Tip:

* Always pair `-d` with `-f` when working with structured files!
    
* Use `cut` for **speed** — it's much faster than `awk` for simple field extractions.
    

## 🧠 `awk` – The Big Brother of `cut` 😎

Welcome the **big brother** of `cut` — the mighty and flexible `awk`. It's not just a command; it's a **powerful text processing language** that can filter, format, compute, and do much more — right from your terminal or within scripts.

> Think of it like `cut` on steroids — if `cut` just extracts fields, `awk` can read, decide, act, calculate, and even talk back 😜.

### 📌 Syntax:

```bash
awk 'pattern {action}' filename
```

* If pattern matches, action is performed.
    
* If no action is given, default action is to **print** the line.
    

### 🧂 Basic Structure Breakdown

```bash
awk -F ':' '{ print $2 }' employees.txt
```

* `-F ':'` → set delimiter as `:`
    
* `$2` → refers to **2nd field** (Name)
    
* `print $2` → prints Name field
    

### ⚙️ Examples on our `employees.txt`

#### 1\. Print all names (2nd column)

```bash
awk -F ':' '{ print $2 }' employees.txt
```

📤 Output:

```bash
Name
Ravi
Anita
...
```

#### 2\. Print Name and Salary

```bash
awk -F ':' '{ print $2, $5 }' employees.txt
```

📤 Output:

```bash
Name Salary
Ravi 75000
Anita 68000
...
```

#### 3\. Print only Developers

```bash
awk -F ':' '$4 == "Developer" { print $2, $4 }' employees.txt
```

📤 Output:

```bash
Ravi Developer
...
```

#### 4\. Print salaries greater than 70000

```bash
awk -F ':' '$5 > 70000 { print $2, $5 }' employees.txt
```

📤 Output:

```bash
Ravi 75000
...
```

#### 5\. Add a title header and format

```bash
awk -F ':' 'BEGIN { print "Name\t\tDept\t\tSalary" }
{ print $2 "\t" $3 "\t\t" $5 }' employees.txt
```

📤 Output:

```bash
Name        Dept        Salary
Ravi        Engineering 75000
...
```

### 🔥 Real Use Cases

✅ **Generate Reports:**

```bash
awk -F ':' '{ sum += $5 } END { print "Total Salary: " sum }' employees.txt
```

✅ **Advanced filtering:**

```bash
awk -F ':' '$3 == "Engineering" && $5 > 70000 { print $2 }' employees.txt
```

✅ **Inline file edits:** Useful in config file processing, logs, parsing JSON-like logs etc.

### 🤯 Bonus: Using `awk` without a file (direct input)

```bash
echo "101:Ravi:Engineering:Developer:75000" | awk -F ':' '{ print $2 }'
```

📤 Output: `Ravi`

## 🔍 `grep` – The Pattern Hunter

The `grep` command is used to **search for patterns (text or regex)** in files or command outputs. Whether you're debugging logs, finding config values, or filtering outputs — `grep` is a Linux hero you’ll use *every single day*.

### 🧠 Basic Syntax:

```bash
grep [options] PATTERN [FILE...]
```

---

### 📘 Examples & Use Cases:

#### 1\. **Search for a word in a file**

```bash
grep "developer" employee_data.txt
```

👉 Shows all lines that contain the word `developer`.

#### 2\. **Case-insensitive search**

```bash
grep -i "developer" employee_data.txt
```

👉 Will match `Developer`, `DEVELOPER`, `developer`, etc.

#### 3\. **Show line numbers**

```bash
grep -n "devops" employee_data.txt
```

👉 Adds line numbers to the matching lines.

#### 4\. **Count matching lines**

```bash
grep -c "Manager" employee_data.txt
```

👉 Returns the number of lines where `Manager` appears.

#### 5\. **Invert match (exclude pattern)**

```bash
grep -v "Admin" employee_data.txt
```

👉 Shows all lines **except** those containing `Admin`.

#### 6\. **Use regex patterns**

```bash
grep "Dev.*" employee_data.txt
```

👉 Matches lines starting with `Dev` and followed by anything (e.g., Developer, DevOps).

#### 7\. **Search recursively in directories**

```bash
grep -r "password" /etc/
```

👉 Useful for searching inside configuration files.

#### 8\. **With pipes to filter command output**

```bash
ps aux | grep ssh
```

👉 Filters running processes to show only those related to `ssh`.

#### 9\. **Highlight matches (colored output)**

```bash
grep --color=auto "admin" employee_data.txt
```

👉 Makes it easier to spot matches.

## 🔹 `egrep` – Extended `grep` for advanced pattern matching

### 📘 Explanation:

`egrep` (short for *extended grep*) is like `grep`, but supports extended regular expressions (ERE). This means you can use more powerful pattern matching without needing to escape special characters like `|`, `+`, `?`, and `()`.

> ✅ Note: Modern systems often treat `egrep` as an alias for `grep -E`.

---

### 🛠️ Syntax:

```bash
egrep [OPTIONS] PATTERN [FILE...]
```

---

### ✅ Common Options:

* `-i`: Case-insensitive search
    
* `-v`: Invert match (show non-matching lines)
    
* `-n`: Show line numbers
    
* `-c`: Count matching lines
    
* `-o`: Show only the matching parts of the line
    
* `--color=auto`: Highlight matches
    

#### 1\. **Find all managers (case-insensitive):**

```bash
egrep -i 'manager' employees.txt
```

🔍 *Explanation*: Matches any line where the role contains “manager”, regardless of case.

#### 2\. **Find people in either HR or Finance:**

```bash
egrep 'HR|Finance' employees.txt
```

🔍 *Explanation*: Uses the `|` operator to match either department.

#### 3\. **Find all roles that end with 'er' (e.g., Developer, Manager):**

```bash
egrep ':.*er:' employees.txt
```

🔍 *Explanation*: Matches lines where the role ends with "er" (using regex with `:` boundaries).

#### 4\. **Find all IDs starting with 10 and ending in 5 or 8:**

```bash
egrep '^10[58]:' employees.txt
```

🔍 *Explanation*: Matches lines where the ID starts with `10` and ends with either `5` or `8`.

#### 5\. **Extract employees with salaries between 60000 and 69999:**

```bash
egrep ':[6][0-9]{4}$' employees.txt
```

🔍 *Explanation*: Matches salaries that start with 6 and are five digits long.

### 💡 Real-world Use Case:

Suppose your HR team needs a list of all executives and managers across departments:

```bash
egrep 'Executive|Manager' employees.txt > leadership.txt
```

This command quickly filters and exports leadership roles to a new file.

## 🔹 `sort` – Arrange lines in a file in a specific order

### 📘 Explanation:

The `sort` command in Linux is used to sort lines in a text file. You can sort alphabetically, numerically, or based on specific fields and delimiters. It's useful for organizing data, finding duplicates, or preparing files for processing.

### 🛠️ Syntax:

```bash
sort [OPTIONS] [FILE]
```

### ✅ Common Options:

* `-n`: Sort numerically
    
* `-r`: Reverse the result of comparisons
    
* `-k`: Sort by a specific key (field)
    
* `-t`: Define a custom delimiter
    
* `-u`: Remove duplicates
    
* `-o FILE`: Write output to a specific file
    

#### 1\. **Sort all entries by name (2nd field):**

```bash
sort -t ':' -k2 employees.txt
```

🔍 *Explanation*: Sorts alphabetically based on the second field (Name).

#### 2\. **Sort by salary (numeric, 5th field):**

```bash
sort -t ':' -k5 -n employees.txt
```

🔍 *Explanation*: Uses colon `:` as delimiter and sorts numerically by salary.

#### 3\. **Sort by department and then by role:**

```bash
sort -t ':' -k3,3 -k4,4 employees.txt
```

🔍 *Explanation*: Sorts first by department (3rd field), then role (4th field).

#### 4\. **Reverse sort by salary:**

```bash
sort -t ':' -k5 -n -r employees.txt
```

🔍 *Explanation*: Highest salary first.

#### 5\. **Sort and remove duplicate lines:**

```bash
sort -u employees.txt
```

🔍 *Explanation*: Removes exact duplicate lines while sorting.

### 💡 Real-world Use Case:

Let’s say you’re doing payroll and want to quickly find out who earns the most and least in your organization. You can use:

```bash
sort -t ':' -k5 -n employees.txt | head -1   # Least paid
sort -t ':' -k5 -n -r employees.txt | head -1  # Highest paid
```

## 🔹 `uniq` – Report or omit repeated lines

### 📘 Explanation:

The `uniq` command filters out **adjacent duplicate lines** in a text file. It’s commonly used to count, filter, or extract unique lines — often in combination with `sort`, since `uniq` only works on **consecutive** duplicates.

### 🛠️ Syntax:

```bash
uniq [OPTIONS] [INPUT [OUTPUT]]
```

### ✅ Common Options:

* `-c`: Prefix lines by the number of occurrences
    
* `-d`: Only print duplicate lines
    
* `-u`: Only print unique lines (non-duplicates)
    
* `-i`: Ignore case when comparing
    
* `-f N`: Skip N fields before comparing
    
* `-s N`: Skip N characters before comparing
    

### ⚠️ Reminder:

You often need to sort before using `uniq`, since it only removes **adjacent** duplicates.

#### 1\. **Get unique department names:**

```bash
cut -d ':' -f3 employees.txt | sort | uniq
```

🔍 *Explanation*: Extracts the department field, sorts them, and removes duplicates.

#### 2\. **Count how many employees in each department:**

```bash
cut -d ':' -f3 employees.txt | sort | uniq -c
```

🔍 *Explanation*: Shows department names along with how many times each appears.

#### 3\. **Find duplicate salaries:**

```bash
cut -d ':' -f5 employees.txt | sort | uniq -d
```

🔍 *Explanation*: Shows salaries that appear more than once.

#### 4\. **List salaries that are unique:**

```bash
cut -d ':' -f5 employees.txt | sort | uniq -u
```

🔍 *Explanation*: Filters out salaries that occur only once.

### 💡 Real-world Use Case:

Suppose HR wants to know how many people share the same salary package to review fairness:

```bash
cut -d ':' -f5 employees.txt | sort | uniq -c | sort -nr
```

This command shows a count of each salary, sorted by frequency, helping spot popular pay bands.

## 🔹 `wc` – Count lines, words, characters, and more

### 📘 Explanation:

`wc` stands for **word count**, but it's more versatile than just counting words. It can count **lines, words, bytes, characters**, and **maximum line length** in one or more files.

### 🛠️ Syntax:

```bash
wc [OPTIONS] [FILE...]
```

### ✅ Common Options:

* `-l`: Count lines
    
* `-w`: Count words
    
* `-c`: Count bytes
    
* `-m`: Count characters
    
* `-L`: Display length of the longest line
    

#### 1\. **Get line, word, and byte count:**

```bash
wc employees.txt
```

🔍 *Explanation*: Shows all three counts (lines, words, bytes) by default.

#### 2\. **Count how many employees (lines in file):**

```bash
wc -l employees.txt
```

📌 Output: `15` — because there are 15 employees (excluding header, if not removed)

#### 3\. **Count number of words in the file:**

```bash
wc -w employees.txt
```

🔍 *Explanation*: Counts all space-separated words — useful for summaries.

#### 4\. **Find total number of characters (including newlines):**

```bash
wc -m employees.txt
```

#### 5\. **Find length of the longest line (e.g., longest record):**

```bash
wc -L employees.txt
```

🔍 *Explanation*: Helpful when formatting output or preparing fixed-width reports.

### 💡 Real-world Use Case:

Imagine you're importing this file into a system that limits the number of characters per line. You could run:

```bash
wc -L employees.txt
```

…to quickly check if any line exceeds the allowed limit and fix it accordingly.

## 🔹 `diff` & `cmp` – Compare files and spot differences

### 📘 Explanation:

Both `diff` and `cmp` are used to compare files, but they serve slightly different purposes:

* `diff`: Compares files line by line and shows the **differences in context**. It’s great for human-readable output.
    
* `cmp`: Compares files **byte by byte** and is faster but less descriptive — better for checking **binary or exact content differences**.
    

## 🛠️ `diff` Syntax:

```bash
diff [OPTIONS] FILE1 FILE2
```

## ✅ `diff` Common Options:

* `-u`: Unified format (common for patches)
    
* `-y`: Side-by-side comparison
    
* `-c`: Context format
    
* `--suppress-common-lines`: Only show differences with `-y`
    

## 📂 Use Cases for `diff`

### 1\. **Compare two versions of employee files:**

```bash
diff employees_v1.txt employees_v2.txt
```

### 2\. **Show unified diff (commonly used in Git):**

```bash
diff -u employees_v1.txt employees_v2.txt
```

### 3\. **Compare side by side:**

```bash
diff -y employees_v1.txt employees_v2.txt
```

🔍 *Explanation*: Highlights line-level changes — great for tracking updates in employee records.

## 🛠️ `cmp` Syntax:

```bash
cmp [OPTIONS] FILE1 FILE2
```

## ✅ `cmp` Common Options:

* `-l`: List all differing bytes
    
* `-b`: Print differing bytes in octal and ASCII
    
* `-i N`: Skip N bytes before comparing
    
* `-n N`: Compare only first N bytes
    

## 📂 Use Cases for `cmp`

### 1\. **Simple file comparison:**

```bash
cmp employees_v1.txt employees_v2.txt
```

📌 Output: `employees_v1.txt employees_v2.txt differ: byte 105, line 3`

### 2\. **Show all differences (byte by byte):**

```bash
cmp -l employees_v1.txt employees_v2.txt
```

🔍 *Explanation*: Useful for checking exact changes — especially with binary or encoded data.

### 💡 Real-world Use Case:

Imagine you're syncing HR records across departments and want to make sure the files are identical:

```bash
cmp employees_main.txt employees_backup.txt && echo "Files match" || echo "Files differ"
```

This simple script automates integrity checking during backups or transfers.

### 🎯 **Wrapping Up Chapter Mastering File & Text Processing**

We’ve just explored some of the **most powerful and versatile tools** in your Linux toolbox.

✅ Extract data columns using `cut`  
✅ Analyze and format text with `awk`  
✅ Search and filter text using `grep`  
✅ Sort and organize data via `sort`  
✅ Eliminate duplicate lines with `uniq`  
✅ Measure files with `wc` (words, lines, characters)  
✅ Compare file contents using `diff` and `cmp`

🔧 **Pro Tip:** Combine these tools in pipelines (using `|`) to build powerful one-liners for data processing. For example:

```bash
cat employees.txt | grep "error" | cut -d":" -f2 | sort | uniq -c
```

This quickly filters, processes, and summarizes logs — ideal for sysadmins and devs!

---

📦 **Up Next: Chapter 10 – Compression, Automation & Editing like a Pro**

Next, you’ll dive into:

✅ Compressing and decompressing files  
✅ Archiving with `tar`  
✅ Splitting and combining large files  
✅ Executing multiple commands with `;`  
✅ Editing files efficiently with `vi`  
✅ Understanding the `vi` vs `vim` difference  
✅ Using `sed` for powerful stream editing

🚀 Get ready to streamline your workflow even more — from working with massive files to editing configs and automating routine tasks like a true Linux power user.

Stay tuned — you're building serious command-line mastery! 💻🔥