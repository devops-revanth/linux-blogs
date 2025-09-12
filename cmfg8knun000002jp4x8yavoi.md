---
title: "Mastering Ansible Adhoc Commands: Understanding Modules and Practical Examples"
seoTitle: "Ansible Adhoc Commands: Modules and Examples"
datePublished: Fri Sep 12 2025 02:46:44 GMT+0000 (Coordinated Universal Time)
cuid: cmfg8knun000002jp4x8yavoi
slug: mastering-ansible-adhoc-commands-understanding-modules-and-practical-examples
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1757645105247/7c176bb1-00a2-43ce-bc70-e75083d487f2.png

---

So far, we’ve explored:  
✅ Why Ansible dominates other configuration tools  
✅ How to install Ansible, set up inventory, and run the first `ping` command

Today, let’s take the next step — **Adhoc commands**.

## 🔹 What are Adhoc Commands?

Ansible Adhoc commands are **one-liners** that let you quickly run tasks on managed nodes **without writing a playbook**. They are great for:

* Quick checks
    
* Testing connectivity
    
* Running simple tasks (restart service, copy a file, install a package, etc.)
    

👉 Syntax of an Adhoc command:

```bash
ansible <host-pattern> -i <inventory> -m <module> -a "<arguments>"
```

Example:

```bash
ansible all -i inventory.ini -m ping
```

## 🔹 Understanding Modules

**Modules are like commands for your Linux machine.**  
Whatever you want to do — install a package, copy a file, create a user — you use a module.

### 📌 Module Evolution in Ansible

* **Ansible ≤ 2.8** → Modules only (`ping`, `yum`, `service`, etc.)
    
* **Ansible 2.9** → Collections introduced (experimental)
    
* **Ansible 2.10+** → Collections became standard; modules moved into collections
    
* **Ansible 2.16 / 2025** → Everything is collection-based, with `ansible.builtin` as the default for core modules
    

👉 Example:

```bash
ansible.builtin.ping
ansible.builtin.copy
amazon.aws.ec2_instance
community.docker.docker_container
```

⚡ By default, if no module is given, Ansible uses the **command** module.

Example:

```bash
ansible all -a "uptime"
```

This uses the **command module** internally.

## 🔹 Commonly Used Modules in Adhoc

Let’s explore key modules with **examples**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757644831881/c85260b4-fa56-4338-a6aa-dab98b7f05d9.png align="center")

### 1️⃣ **Ping Module**

Used to check connectivity.

```bash
ansible all -i inventory.ini -m ansible.builtin.ping
```

✅ Returns `pong` if successful.

### 2️⃣ **Command vs Shell Module**

* **command** → Safest, runs commands directly (no shell features).
    
* **shell** → Runs inside a shell (supports pipes `|`, redirects `>`, variables).
    

👉 Example (command):

```bash
ansible all -i inventory.ini -m command -a "uptime"
```

👉 Example (shell):

```bash
ansible all -i inventory.ini -m shell -a "echo $HOME"
```

⚡ Rule of thumb: use `command` unless you really need shell features.

## 3️⃣ Adhoc Examples with `file` Module

### 1\. Create a Directory

```bash
ansible all -m file -a "path=/tmp/mydir state=directory"
```

* `path` → where to create.
    
* `state=directory` → ensures it’s a directory.
    

### 2\. Create an Empty File

```bash
ansible all -m file -a "path=/tmp/myfile.txt state=touch"
```

* `state=touch` → creates an empty file (like `touch` command).
    

### 3\. Set File Permissions (mode)

```bash
ansible all -m file -a "path=/tmp/myfile.txt mode=0644"
```

* `mode=0644` → owner can read/write, group & others can only read.
    

### 4\. Change Ownership (user & group)

```bash
ansible all -m file -a "path=/tmp/myfile.txt owner=ansible group=devops"
```

* Changes file owner and group.
    

### 5\. Remove a File

```bash
ansible all -m file -a "path=/tmp/myfile.txt state=absent"
```

* `state=absent` → deletes the file.
    

### 6\. Remove a Directory

```bash
ansible all -m file -a "path=/tmp/mydir state=absent"
```

* Removes directory if it exists.
    

### 4️⃣ **File-Related Modules**

* **copy** → Copy files from control node → managed node
    

```bash
ansible all -i inventory.ini -m ansible.builtin.copy -a "src=/etc/hosts dest=/tmp/hosts"
```

* **lineinfile** → Add/modify a line inside a file
    

```bash
ansible all -i inventory.ini -m ansible.builtin.lineinfile -a "path=/tmp/testfile line='Hello from Ansible'"
```

* **stat** → Check file details
    

```bash
ansible all -i inventory.ini -m ansible.builtin.stat -a "path=/etc/passwd"
```

* **fetch** → Copy files from managed node → control node
    

```bash
ansible all -i inventory.ini -m ansible.builtin.fetch -a "src=/etc/passwd dest=/tmp/"
```

### 5️⃣ **Package Modules**

* **dnf** (RHEL), **apt** (Ubuntu), **package** (generic).
    

👉 Install Apache (RHEL):

```bash
ansible all -i inventory.ini -m ansible.builtin.dnf -a "name=httpd state=present"
```

👉 Install Apache (Ubuntu):

```bash
ansible all -i inventory.ini -m ansible.builtin.apt -a "name=apache2 state=present"
```

👉 Using generic `package`:

```bash
ansible all -i inventory.ini -m ansible.builtin.package -a "name=curl state=present"
```

### 6️⃣ **Service/Systemd Modules**

* **service** → Manage services
    

```bash
ansible all -i inventory.ini -m ansible.builtin.service -a "name=httpd state=started"
```

* **systemd** → Manage systemd units
    

```bash
ansible all -i inventory.ini -m ansible.builtin.systemd -a "name=httpd enabled=yes state=started"
```

### 7️⃣ **Debug Module**

Used mostly in playbooks, but can be run via adhoc too.

```bash
ansible all -i inventory.ini -m ansible.builtin.debug -a "msg='Hello from Adhoc'"
```

### 8️⃣ **User Module**

Create users on managed nodes.

```bash
ansible all -i inventory.ini -m ansible.builtin.user -a "name=devops state=present"
```

## ✨ Wrap-Up

In this blog, we:

* Understood **modules** and how they evolved into **collections**
    
* Learned **command vs shell** module difference
    
* Explored **key modules** with **adhoc command examples**: `ping`, `file` , `copy`, `lineinfile`, `stat`, `fetch`, `package`, `service/systemd`, `debug`, `user`
    

👉 Adhoc commands are great for **quick automation**, but in real-world use cases, you’ll eventually move to **Playbooks** for structured, repeatable tasks.