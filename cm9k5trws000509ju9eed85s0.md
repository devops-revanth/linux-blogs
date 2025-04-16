---
title: "Practice for Revanth Part 1"
datePublished: Mon Mar 31 2025 18:30:00 GMT+0000 (Coordinated Universal Time)
cuid: cm9k5trws000509ju9eed85s0
slug: practice-for-revanth

---

# 🐧 Linux Installation → 🔧 File Management

## 🐧 Linux Basics – Filesystem & Navigation

### 📦 Linux Server Installation

### 🔐 Server Access

---

## 📁 What is a File System?

A file system organizes and stores data on storage devices.

---

## 📂 File System Structure & Navigation

### 🔍 Basic Navigation Commands

```bash
pwd            # Print working directory  
cd Documents   # Navigate into Documents folder  
cd ..          # Go one level up  
cd ~           # Go to home directory  
ls             # List files and directories
```

---

## 🧭 File System Paths

### 📍 Absolute Path

```bash
/home/alex/Documents/example.txt
```

### 📍 Relative Path

```bash
Documents/example.txt
```

---

## 📄 Types of Files

| Symbol | Type | Description |
| --- | --- | --- |
| `-` | Regular File | Text, images, scripts, binaries |
| `d` | Directory | Folders that contain files or other directories |
| `l` | Symbolic Link (Soft) | Shortcut or reference to another file |
| `c` | Character Device File | Data transferred character by character (e.g. keyboard) |
| `s` | Socket File | IPC via network communication |
| `p` | Named Pipe (FIFO) | IPC – first-in-first-out communication |
| `b` | Block Device File | Data transferred in blocks (e.g. hard drives) |

```bash
ls -l /dev
```

---

## 📌 Inode

An inode contains metadata about a file:

* File type
    
* Permissions
    
* Owner and group
    
* Size
    
* Timestamps (created, modified, accessed)
    
* Pointers to data blocks
    

---

## 🔗 Hard Link Example

```bash
echo "This is the original file" > file1
ln file1 file2
ls -li file1 file2
stat file1
stat file2
```

Diagram:  
**file2 ➝ Inode ➝ Data blocks on disk**

---

## 🔗 Symbolic (Soft) Link Example

```bash
echo "This is the original file" > file1
ln -s file1 file2
ls -li file1 file2
stat file1
stat file2
```

Diagram:  
**file2 ➝ Path ➝ file1 ➝ Inode ➝ Data blocks**

---

## 🧪 Check Links with `ls -l`

```bash
ls -l
```

---

# 🧰 File System Operations & Finding Files in Linux

---

## 🛠️ File System Operations

### 📄 Create Files & Directories

```bash
touch file1.txt         # Create an empty file  
cp file1.txt file2.txt  # Copy file  
vi file3.txt            # Create/edit file using vi editor  
mkdir myfolder          # Create a directory  
cp -R myfolder newfolder  # Recursively copy a directory
```

---

## 🔎 Finding Files – `find`, `locate`

### 🔍 Basic `find` Usage

```bash
find <path> <criteria> <action>
```

#### Examples:

```bash
find . -name file1.txt
find / -iname file1.txt
find /home -type f -name "*.txt"
find / -type d -name project
find /var/log -mtime -1
find / -type f -size +100M
find /tmp -type f -name "*.tmp" -delete
```

---

### 🛡️ Special Permission-Based Searches

#### 🔴 Files with SUID

```bash
find / -type f -perm -4000 -exec cp --parents {} /tmp/special-perms/suid/ \; 2>/dev/null
```

#### 🟡 Files with SGID

```bash
find / -type f -perm -2000 -exec cp --parents {} /tmp/special-perms/sgid/ \; 2>/dev/null
```

#### 🔵 Files/Dirs with Sticky Bit

```bash
find / -type f -perm -1000 2>/dev/null     # Files
find / -type d -perm -1000 2>/dev/null     # Directories (more common)
```

---

### 📁 Ownership & Permissions

```bash
find /home -user revanth
find /srv -group devops
find /var/www -type f ! -perm -002         # Not writable by others
```

---

### 🧹 Clean-Up & Maintenance

```bash
find /var/log -type f -empty
find /home -type d -empty
find / -xtype l 2>/dev/null                 # Broken symbolic links
find /var/log -name "*.log" -mtime +7 -delete
find / -inum 123456                         # Specific inode
find / -type f -links +1                    # Hard link count > 1
find /var/log -name "*.log" -size +100M -exec gzip {} \;
find /var -type d -exec du -sh {} + 2>/dev/null | sort -rh | head
find /etc -type f -name "*.conf" -user root -mtime -5 -size +10k
```

---

## ⚡ `locate` – Super Fast File Search (Using Cache)

```bash
locate file1.txt
locate log                    # Search for 'log' files
updatedb                      # Update locate database
```

### 🔧 Install `mlocate` (if not already installed)

```bash
rpm -qa | grep mlocate
yum install mlocate -y
```

# 🗂️ File Permissions, Ownership, ACL & Special Permissions

---

## 🔒 How Do We See File Permissions?

```bash
ls -l                      # List files with permissions  
ls -l raju                 # Check permissions for a specific file/folder
```

### 🧑‍💻 Permission Breakdown

Permissions are displayed as:

```bash
-rw-r--r--
```

This means:

* **User (u)**: `rw-` → Read & Write
    
* **Group (g)**: `r--` → Read
    
* **Others (o)**: `r--` → Read
    

---

## 🛠️ Modifying Permissions with `chmod`

* **Add permission**:
    
    ```bash
    chmod g+w raju       # Adds write permission to group
    chmod u-w raju       # Removes write permission from user
    chmod a-r raju       # Removes read permission from all
    chmod u+rw raju      # Adds read and write permission for user
    chmod g+rw raju      # Adds read and write permission for group
    chmod o+r raju       # Adds read permission for others
    ```
    

### 📝 Numeric (Octal) Mode:

* `r = 4`, `w = 2`, `x = 1`
    

Examples:

```bash
chmod 444 raju   # Read-only for everyone
chmod 644 raju   # rw for user, r for group and others
chmod 755 raju   # rwx for user, rx for group and others
chmod -R 755 /dir # To change permissions for all files in a directory
```

---

## 👥 Ownership Commands

* **Change ownership**:
    
    ```bash
    sudo chown raju file.txt            # Change owner to 'raju'
    sudo chown raju:devops file.txt     # Change owner to 'raju' and group to 'devops'
    sudo chown -R raju:devops /project  # Recursively change ownership in /project
    ```
    
* **Change group**:
    
    ```bash
    chgrp devops file.txt               # Change group to 'devops'
    chgrp -R devops /project            # Recursively change group in /project
    ```
    
* **Change ownership of a directory and all its contents**:
    
    ```bash
    chown -R username:groupname /dir
    ```
    

---

## 🛡️ Access Control Lists (ACL)

### 💾 Set ACL Permissions

* **Set user-specific ACL**:
    
    ```bash
    setfacl -m u:username:--- /home/raju
    ```
    
* **Create a secure directory**:
    
    ```bash
    mkdir /secure
    chmod 700 /secure
    mv important_file.txt /secure/
    ```
    
* **Add user/group ACL permissions**:
    
    ```bash
    setfacl -m u:john:rwx test        # Give rwx permission to a user
    setfacl -m g:developers:rw test    # Give read-write permission to a group
    ```
    
* **Recursive ACL (inherit ACL from directory)**:
    
    ```bash
    setfacl -Rm u:john:rw /dir
    ```
    
* **Remove specific ACL entry**:
    
    ```bash
    setfacl -x u:john test
    ```
    
* **Remove all ACL entries**:
    
    ```bash
    setfacl -b test
    ```
    
* **View ACL entries**:
    
    ```bash
    getfacl test
    ```
    

### I/O Redirection, `tee`, and Pipes in Linux

#### Basic Redirection

* **Standard Output (stdout)** redirection:
    
    ```bash
    echo "Hello" > linux  # Redirects stdout to a file, overwrites the file if it exists.
    echo "How are you" >> linux  # Appends stdout to the file.
    ```
    
* **Using** `cat` to create and write to a file:
    
    ```bash
    cat > linux  # Will create and write to the file "linux".
    ```
    
* **Editing the file with** `vi`:
    
    ```bash
    vi linux  # Open the file for editing in vi.
    ```
    

#### Input/Output Redirection

* **Redirect stdout to a file:**
    
    ```bash
    echo "Hello" > linux  # > → Redirect stdout (Overwrites file)
    ```
    
* **Append stdout to a file:**
    
    ```bash
    echo "How are you" >> linux  # >> → Append to file (stdout)
    ```
    
* **Redirect stderr (errors only) to a file:**
    
    ```bash
    ls wrong_file 2> error.txt  # 2> → Redirect stderr to a file
    ```
    
* **Append stderr to a file:**
    
    ```bash
    ls wrong_folder 2>> error.txt  # 2>> → Append stderr to a file
    ```
    
* **Redirect both stdout and stderr to the same file:**
    
    ```bash
    ls /etc /wrong_folder &> all_output.txt  # &> → Redirect both stdout & stderr (Bash only)
    ```
    
* **Separate stdout and stderr into different files:**
    
    ```bash
    ls /etc /wrong_folder > output.txt 2> error.txt  # Separate stdout and stderr
    cat output.txt error.txt  # Display the contents of both files
    ```
    
* **Redirect stderr to the same location as stdout:**
    
    ```bash
    ls /etc /wrong_folder 1> combined.txt 2>&1  # 2>&1 → Send stderr to same place as stdout
    ```
    
* **Incorrect redirection:**
    
    ```bash
    ls /etc /wrong_folder 2>&1 > wrong.txt  # Wrong Version of Redirection
    ```
    

#### Using `/dev/null`

* **Suppressing output and only logging errors:**
    
    ```bash
    rsync -av /source /backup > /dev/null 2>&1  # Redirect both stdout & stderr to /dev/null
    find / -name "*.conf" > /dev/null 2> error.log  # Logging only errors, ignoring successful output
    ls /etc/ /wrong_folder > files.txt 2> /dev/null  # Capturing only output, ignoring errors
    ```
    
* **Checking file existence without showing output:**
    
    ```bash
    test -f somefile.txt > /dev/null 2>&1 && echo "Exists" || echo "No file"
    [ -f somefile.txt ] && echo "Found" || echo "Not found"
    ```
    

#### Using `tee`

* **Viewing and saving output simultaneously:**
    
    ```bash
    echo "Hello" | tee log.txt  # View & Save Output Simultaneously
    ```
    
* **Appending to a file instead of overwriting:**
    
    ```bash
    echo "Line2" | tee -a log.txt  # Append to a file
    ```
    
* **Using** `tee` with other commands in a pipeline:
    
    ```bash
    df -h | tee disk.txt | grep /dev  # Combine with Other Commands (Pipeline Magic)
    ```
    
* **Using** `tee` with `sudo` to edit root-owned files:
    
    ```bash
    echo "new config" | sudo tee -a /etc/config.conf  # Edit root-owned files
    ```
    
* **Logging script output while monitoring:**
    
    ```bash
    ./backup.sh | tee backup_log.txt  # Log script output while monitoring
    ```
    

#### Using Pipes

* **Filter processes with** `grep` via pipes:
    
    ```bash
    ps aux | grep ssh  # List processes and filter with grep
    ```
    
* **Count the number of lines in a file:**
    
    ```bash
    cat file.txt | wc -l  # Count lines in file using cat and pipe
    wc -l < file.txt  # An alternative to count lines in file
    ```
    
* **Monitor a log file for errors using** `tail` and `grep`:
    
    ```bash
    tail -f /var/log/syslog | grep "error"  # Follow syslog and filter errors
    ```
    
* **Process a file with** `cut`, `sort`, and `uniq`:
    
    ```bash
    cat /etc/passwd | cut -d: -f1 | sort | uniq  # Extract and process file content
    ```
    
* **Display disk space and filter specific information:**
    
    ```bash
    df -h | tee disk_log.txt | grep /dev  # View and save disk space information
    ```
    

## 🛠️ File Maintenance & Display Commands in Linux

### 📁 File Maintenance Commands

* **Create Files & Directories**
    
    ```bash
    touch file1.txt                    # Create an empty file
    vi file2.txt                       # Open file in vi editor
    mkdir test                         # Create a directory
    mkdir -p /tmp/parent/child         # Create nested directories
    mkdir -p projectname/{docs,src,tests}  # Create a project structure with multiple folders
    ```
    
* **Delete Files & Directories**
    
    ```bash
    rm file1.txt                       # Remove a file
    rm -f file2.txt                    # Force remove without prompt
    rm -rf /tmp/parent                 # Remove directory recursively
    rmdir emptydir                     # Remove empty directory
    ```
    
* **Copy & Move Files**
    
    ```bash
    cp file1.txt file2.txt             # Copy file
    cp -i file2.txt file3.txt          # Interactive copy (prompt before overwrite)
    cp -p file1.txt file4.txt          # Preserve file attributes
    cp -r docs/ backup_docs/           # Recursively copy directory
    mv file4.txt archive.txt           # Rename or move file
    mv archive.txt /tmp/               # Move file to another directory
    mv projectname/ /home/user/        # Move directory to a new location
    cp -r template_project/ new_project/  # Copy entire project
    mv *.txt docs/                     # Move all .txt files to docs
    rm -f *.log                        # Remove all log files
    mkdir -p myproject/{src,tests,docs,build}  # Full project structure
    cp -p *.conf backup_configs/       # Backup all config files
    rm -rf temp_build/                 # Clean temp builds
    ```
    

---

### 📄 File Display Commands

* **Basic Display**
    
    ```bash
    cat filename                       # Display entire file
    cat hello.txt
    cat file1.txt file2.txt > combined.txt  # Combine files into one
    ```
    
* **Paging Through Files**
    
    ```bash
    less filename                      # Scroll with PageUp/PageDown
    less /var/log/syslog
    more filename                      # Basic pager
    more /etc/services
    ```
    
* **Show Specific Parts**
    
    ```bash
    head filename                      # First 10 lines
    head -n 20 filename                # First 20 lines
    tail filename                      # Last 10 lines
    tail -n 20 filename                # Last 20 lines
    tail -f /var/log/syslog            # Follow file updates in real time
    tail -f /var/log/syslog | grep -i error  # Watch for live errors
    ```
    
* **Power Usage**
    
    ```bash
    head -n 1 config.yaml              # Show first line only
    cat log.txt | grep "2025-04-12" | head -n 10
    ./backup.sh | tee backup.log | tail -n 10  # Monitor and log script output
    diff old_config.conf new_config.conf | less
    cat report.csv | column -t -s "," | less   # Format CSV neatly
    ```
    

---

## 📑 Text Processing in Linux

### ✂️ `cut` – Extract Specific Columns

```bash
cut -d ":" -f2 employees.txt               # Extract Names (2nd field)
cut -d ":" -f2,3 employees.txt             # Name & Department
cut -d ":" --complement -f5 employees.txt  # All fields except Salary
cut -c 1-5 employees.txt                   # First 5 characters of each line
cut -d ' ' -f1 /var/log/nginx/access.log   # IP addresses from logs
cut -d ":" -f2,5 employees.txt             # Name and Salary
```

---

### 🧠 `awk` – Advanced Text Processing

```bash
awk -F ':' '{ print $2 }' employees.txt                         # Just Names
awk -F ':' '{ print $2, $5 }' employees.txt                     # Name and Salary
awk -F ':' '$4 == "Developer" { print $2, $4 }' employees.txt   # Only Developers
awk -F ':' '$5 > 70000 { print $2, $5 }' employees.txt          # Salaries > 70000
awk -F ':' 'BEGIN { print "Name\tDept\tSalary" } 
{ print $2 "\t" $3 "\t" $5 }' employees.txt                     # Nicely formatted
awk -F ':' '{ sum += $5 } END { print "Total Salary: " sum }' employees.txt
awk -F ':' '$3 == "Engineering" && $5 > 70000 { print $2 }' employees.txt
echo "101:Ravi:Engineering:Developer:75000" | awk -F ':' '{ print $2 }'
```

---

### 🔍 `grep` & `egrep` – Search Text Patterns

```bash
grep -i "developer" employee_data.txt
grep -n "devops" employee_data.txt
grep -c "Manager" employee_data.txt
grep -v "Admin" employee_data.txt
grep "Dev.*" employee_data.txt
grep -r "password" /etc/
ps aux | grep ssh
grep --color=auto "admin" employee_data.txt

egrep -i 'manager' employees.txt
egrep 'HR|Finance' employees.txt
egrep ':.*er:' employees.txt
egrep '^10[58]:' employees.txt
egrep ':[6][0-9]{4}$' employees.txt
egrep 'Executive|Manager' employees.txt > leadership.txt
```

---

### 🔃 `sort`, `uniq` – Sorting & Uniqueness

```bash
sort -t ':' -k2 employees.txt              # Sort by Name
sort -t ':' -k5 -n employees.txt           # Sort by Salary
sort -t ':' -k3,3 -k4,4 employees.txt      # Dept + Role
sort -t ':' -k5 -n -r employees.txt        # Descending Salary
sort -u employees.txt                      # Unique lines

# Salary Insights
sort -t ':' -k5 -n employees.txt | head -1         # Least paid
sort -t ':' -k5 -n -r employees.txt | head -1      # Highest paid

# Frequency Analysis
cut -d ':' -f3 employees.txt | sort | uniq
cut -d ':' -f3 employees.txt | sort | uniq -c
cut -d ':' -f5 employees.txt | sort | uniq -d
cut -d ':' -f5 employees.txt | sort | uniq -u
cut -d ':' -f5 employees.txt | sort | uniq -c | sort -nr
```

---

### 📊 `wc`, `diff`, `cmp` – Count & Compare

```bash
wc employees.txt             # Lines, Words, Characters
wc -l employees.txt          # Just line count
wc -w employees.txt          # Word count
wc -m employees.txt          # Character count
wc -L employees.txt          # Length of longest line

diff employees_v1.txt employees_v2.txt
diff -u employees_v1.txt employees_v2.txt
diff -y employees_v1.txt employees_v2.txt

cmp employees_v1.txt employees_v2.txt
cmp -l employees_v1.txt employees_v2.txt
cmp employees_main.txt employees_backup.txt && echo "Files match" || echo "Files differ"

# Combo command: Find, cut, sort and count unique errors
cat employees.txt | grep "error" | cut -d":" -f2 | sort | uniq -c
```

## 🗜️ File Compression & Automation

### 📦 `tar`, `gzip`, `gunzip`

```bash
tar -cvf archive.tar file1 file2 dir/
tar -cvf project_backup.tar file1.txt file2.txt dir1/
gzip file1.txt
tar -czvf backup.tar.gz file1.txt dir/
gunzip file1.txt.gz
gzip -d file1.txt.gz
tar -xzvf backup.tar.gz
tar -czvf project_2024.tar.gz /home/user/project/
gzip access.log
scp access.log.gz user@server:/logs/
tar -czvf logs.tar.gz /var/log/*.log
scp logs.tar.gz user@remote:/backup/logs/
tar -tzvf backup.tar.gz
```

### 📂 Combine & Split Files

```bash
cat part1.txt part2.txt part3.txt > fullfile.txt
cat notes1.txt >> master_notes.txt
cat notes2.txt >> master_notes.txt
cat log-2025-04-*.log > weekly-report.log
split -b 100M bigbackup.tar.gz backup_
split -l 1000 bigfile.csv chunk_
cat backup_* > bigbackup.tar.gz
zip --split-size 100m -r project.zip project/
```

### ⚙️ Command Chaining – `;`, `&&`, `||`

```bash
echo "Cleaning..." ; rm -rf temp/ ; echo "Done!"
mkdir project && cd project && touch readme.md
mkdir project && cd notfound && echo "Ready"
rm important.txt || echo "Could not delete file"
mkdir dir1 && echo "Created" || echo "Failed"
mkdir dir1 && { echo "Created"; } || echo "Failed"
tar -czf backup.tar.gz /mydata && mv backup.tar.gz /backup || echo "Backup failed!"
ls ; wrongcmd ; echo "End"
ls && wrongcmd && echo "Final step"
mkdir logs && { cd logs && touch log.txt; } || echo "Something went wrong"
```

### ✍️ `vi` and `vim`

```bash
vi filename
vim anotherfile.txt
```

### 🧹 `sed` – Stream Editor

```bash
sed 's/old_pattern/new_pattern/' file
sed 's/apple/orange/' fruits.txt
sed 's/apple/orange/g' fruits.txt
sed 's/apple/orange/i' fruits.txt
sed '/banana/d' fruits.txt
sed '/apple/i This is a fruit' fruits.txt
sed '/banana/a Fruit end' fruits.txt
sed -n '/apple/p' fruits.txt
sed '/^#/d' access.log > clean_log.txt
sed 's/\([0-9]\{1,3\}\.\){3}[0-9]\{1,3\}/xxx.xxx.xxx.xxx/g' clean_log.txt > anonymized_log.txt
sed -n '/ 404 /p' anonymized_log.txt > error_404_log.txt
```