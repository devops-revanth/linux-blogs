---
title: "Getting Started with Ansible Playbooks: What, Why, and Writing Your First One"
seoTitle: "Beginner's Guide to Ansible Playbooks"
datePublished: Fri Sep 12 2025 06:21:33 GMT+0000 (Coordinated Universal Time)
cuid: cmfgg8x44000t02l410z5bgom
slug: getting-started-with-ansible-playbooks-what-why-and-writing-your-first-one
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1757658066481/18ebe678-c891-4ae3-b22c-01b5d1ed6fbd.png

---

So far, we’ve covered a lot in our Ansible journey:

* **Shell vs Ansible** – why Ansible is better for automation
    
* **Why Ansible dominates other configuration tools**
    
* **Installing Ansible and running your first Adhoc command**
    
* **Mastering Adhoc commands and modules**
    

Today, we’re taking the next step by diving into **Ansible Playbooks** — the real backbone of structured automation.

## 🔹 Why Playbooks?

If you remember our previous blog on Adhoc commands, you know they’re fantastic for quick, one-off tasks. But what if you need:

* Repetitive tasks
    
* Multi-step operations
    
* Reusable automation scripts
    

That’s where **Playbooks** come in.

Think of it this way: Adhoc commands are like sending a single email manually. Playbooks are like writing an automated email sequence that runs exactly the same way every time, across multiple recipients.

**Key advantages of Playbooks:**

* **Reusable** → Run multiple times without breaking the system
    
* **Structured & readable** → Tasks are clearly organized in YAML
    
* **Idempotent** → Ensures the same result no matter how many times you run it
    
* **Supports advanced features** → Variables, loops, conditionals, handlers, and roles
    

In short: **Playbooks turn your manual commands into reliable automation scripts.**

## 🔹 What is a Playbook?

An **Ansible Playbook** is simply a YAML file that defines a set of automation tasks to execute on one or more hosts.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757657297812/e69d2e48-a880-446b-9665-c23284ebf866.png align="center")

**Key points to remember:**

* Written in **YAML**
    
* Tasks run **sequentially**
    
* If a task fails, the playbook stops unless `ignore_errors: yes` is used
    
* Can target **one or multiple hosts**
    
* Ensures **idempotency**
    

## 🔹 Writing Your First Playbook: Ping Your Servers

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757657492708/fb94d904-31b8-435c-b9f0-2e064617f754.png align="center")

Let’s start simple and ping our servers to test connectivity.

**ping.yaml**

```yaml
- name: PING
  hosts: web
  tasks:
    - name: ping the server
      ansible.builtin.ping:
```

**Explanation:**

* **name** → Friendly description of the play (“PING”)
    
* **hosts** → Target machines (here, `web`)
    
* **tasks** → List of tasks using modules (here, `ansible.builtin.ping`)
    

## 🔹 Inventory Refresher

Remember our previous blog about inventory files ([Check it here](https://blog.durgarevu.com/getting-started-with-ansible-installation-control-node-communication-and-first-adhoc-command))? Playbooks rely heavily on them.

An **inventory file** tells Ansible which servers to manage. You can define **groups** for easier management:

```ini
[web]
web01.example.com
web02.example.com

[db]
db01.example.com
db02.example.com

[allservers:children]
web
db
```

* `[web]` → all web servers
    
* `[db]` → all database servers
    
* `[allservers:children]` → combined group
    

In our ping playbook, `hosts: web` targets only the web servers. Later, if you want to ping all servers, just change it to `hosts: allservers`.

**Why groups are useful:**

* Run playbooks on a subset of servers
    
* Organize servers by role (web, db, cache)
    
* Combine groups to execute tasks across multiple roles
    

This keeps your playbooks **flexible, reusable, and readable** — exactly what Ansible is designed for.

## 🔹 Running Your First Playbook

Once you’ve written your playbook and have your inventory file ready:

```bash
ansible-playbook -i inventory.ini ping.yaml
```

✅ You’ll see output showing success for each server in your target group.

## ✨ Wrap-Up

* Adhoc commands are great for quick tasks, but Playbooks are for **reusable, structured automation**
    
* Playbooks use **YAML**, target one or multiple hosts, and ensure **idempotency**
    
* Your first playbook can be as simple as a **ping test**, but this lays the foundation for complex automation
    
* In future blogs, we’ll explore **variables, loops, conditionals, handlers, and roles** in playbooks
    

Until then, start experimenting with your first playbook and see the magic of automation in action! 🚀