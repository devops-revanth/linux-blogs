---
title: "Practice for Revanth"
datePublished: Wed Apr 16 2025 16:42:41 GMT+0000 (Coordinated Universal Time)
cuid: cm9k5trws000509ju9eed85s0
slug: practice-for-revanth

---

# 🐧 Linux Basics – Filesystem & Navigation

## 📦 Linux Server Installation

## 🔐 Server Access

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