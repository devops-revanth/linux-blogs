---
title: "Getting Started with Ansible: Installation, Control Node Communication, and First Adhoc Command"
seoTitle: "Ansible Basics: Setup and First Command"
datePublished: Wed Sep 10 2025 04:10:44 GMT+0000 (Coordinated Universal Time)
cuid: cmfdgoz6g000002k03z97bvg5
slug: getting-started-with-ansible-installation-control-node-communication-and-first-adhoc-command
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1757477408963/ca3892f5-5bcd-4d46-8697-23021368dd39.png

---

So far, we’ve discussed **Shell vs Ansible** and explored **why Ansible dominates other configuration tools**.

Now, as the next step, let’s move into something more hands-on:  
✅ Installing Ansible  
✅ Understanding how the **Control Node** communicates with **Managed Nodes**  
✅ Running your very first **Adhoc command**

## 🖥️ What is Control Node and Managed Node?

* **Control Node** → The server where you install Ansible. This is the brain of your automation.
    
* **Managed Nodes** → The clients (servers) that Ansible manages and configures.
    

Ansible works on a **push-based model**. The Control Node pushes tasks to Managed Nodes using **SSH** (no agents required).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757477090202/003fdef2-9fb8-42fa-99d3-11ffe766e9c1.png align="center")

## ⚙️ Installing Ansible

Ansible provides detailed instructions depending on your OS:  
🔗 [Official Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

Here, let’s walk through the **installation on RHEL** using **pip**.

### 1\. Verify pip

```bash
python3 -m pip -V
```

✅ If you see a version, pip is available.  
❌ If not, install pip:

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3 get-pip.py --user
```

### 2\. Install Ansible

```bash
python3 -m pip install --user ansible
```

👉 Minimal package:

```bash
python3 -m pip install --user ansible-core
```

👉 Specific version:

```bash
python3 -m pip install --user ansible-core==2.12.3
```

👉 Upgrade to latest:

```bash
python3 -m pip install --upgrade --user ansible
```

### 3\. Verify Installation

```bash
ansible --version
ansible-community --version
```

**Sample Output:**

```bash
ansible [core 2.15.13]
python version = 3.9.18
jinja version = 3.1.6
Ansible community version 8.7.0
```

🎉 Ansible is now ready!

## 📂 Ansible Inventory

Ansible needs to know *which servers to manage*. This is defined in an **inventory file**.

* Default location: `/etc/ansible/hosts`
    
* Can also use custom inventory files (INI or YAML formats).
    

👉 Example: `inventory.ini`

```ini
3.91.219.180 ansible_user=ec2-user ansible_password=DevOps321
```

## 🚀 Running Your First Adhoc Command

Let’s use the **ping module** to test communication between the Control Node and Managed Node:

```bash
ansible -i inventory.ini -m ansible.builtin.ping all
```

✅ Expected output:

```json
3.91.219.180 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

🎉 Congratulations! You just ran your first Ansible command.

## ✨ Wrap-Up

* We installed Ansible using `pip`.
    
* Understood the **Control Node ↔ Managed Node** relationship.
    
* Learned about **inventory files**.
    
* Successfully ran our **first Adhoc command**.
    

👉 **“This is just the beginning — next, we’ll dive deeper into Ansible to automate real-world tasks in a structured way.”**