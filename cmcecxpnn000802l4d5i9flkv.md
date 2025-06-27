---
title: "🧱Chapter 2 : Linux Practical Series – : Working with Files and Directories"
datePublished: Fri Jun 27 2025 05:14:12 GMT+0000 (Coordinated Universal Time)
cuid: cmcecxpnn000802l4d5i9flkv
slug: chapter-2-linux-practical-series-working-with-files-and-directories

---

> **Continuing the 100% practical Linux learning path — no theory, just real tasks.**

## 🔁 Recap from Chapter 1

In the last chapter, you learned how to:

* Log into your system
    
* Explore system information
    
* Create folders and files
    
* Move files across directories
    
* Script your work
    

Now it’s time to go deeper — **manipulate files and directories**, **view content**, and **practice path navigation**.

## ✅ **Tasks to Perform – Chapter 2**

### 1️⃣ Listing & Navigation

* Check which directory you're currently in.
    
* List all files and directories (both visible and hidden) from your current location.
    
* Navigate to different folders using both relative and absolute paths.
    

### 2️⃣ Create and Manage More Files

* Create a few more test files in various folders.
    
* Try the following actions:
    
    * Rename a file
        
    * Move a file from one directory to another
        
    * Copy a file into multiple locations
        
    * Delete a file
        

### 3️⃣ Understand rm vs rmdir

* Try removing files using `rm`.
    
* Try removing empty directories using `rmdir`.
    
* Observe the difference between the two and what happens with non-empty directories.
    

### 4️⃣ View and Modify File Contents

* Open a file and **view its contents**.
    
* Add **a few more lines** to an existing file.
    
* Count how many lines are in a given file.
    
* Practice viewing files using both **absolute** and **relative paths**.
    

### 5️⃣ Final Step – Script Your Work

* Record all the commands you used into a script file.
    
* Execute the script to replicate your actions in one go.
    

## 🎁 **Bonus Task – Handling Files with Spaces in Names**

Let’s make things a bit more interesting.

You’ll often come across (or accidentally create) files with spaces in their names. While this is possible in Linux, it's not ideal — and managing them from the command line can get tricky.

### 🔧 Task:

1. Create a few files with spaces in their names like:
    
    * `project report.txt`
        
    * `daily notes.txt`
        
    * `linux guide.doc`
        
2. Try listing and opening these files — notice how you have to handle the spaces.
    
3. Now, **rename each file** to remove the spaces or replace them with underscores or hyphens, like:
    
    * `project_report.txt`
        
    * `daily-notes.txt`
        
4. Try doing this **manually** at first, then explore how to automate this renaming using:
    
    * `mv` in a loop
        
    * `rename` command *(if available)*
        

💡 *This exercise will improve your handling of special characters and teach you good file-naming practices for scripting and automation.*

## 📌 Commands You Will Use (Not Explained – Find Them Yourself)

`ls`, `cd`, `pwd`, `mv`, `rm`, `rmdir`, `cat`, `echo`, `touch`, `cp`, `vi`, `wc`

## 🧠 Goal

By the end of this chapter, you should be **comfortable working with files and directories**, managing content, navigating using different paths, and writing repeatable scripts.

## 📝 **Chapter 2 Summary – Working with Files and Directories**

In Chapter 2, you expanded on your foundational Linux skills by focusing on practical file and directory operations. You learned how to list and navigate through the file system using both absolute and relative paths.

You practiced creating new files, renaming them, moving them across directories, and deleting them using `rm` and `rmdir` — understanding the key differences between file and directory removal. You also viewed and modified file contents, added new lines, and counted the number of lines in a file.

To wrap up, you created a script that replicated all the tasks you performed, reinforcing the habit of automation and repeatability — just like a real Linux administrator.