---
title: "Ansible Playbooks: Writing Multiple Tasks and Plays in a Single Playbook"
seoTitle: "Writing Multiple Tasks in Ansible Playbooks"
datePublished: Mon Sep 15 2025 02:21:27 GMT+0000 (Coordinated Universal Time)
cuid: cmfkhzozi000d02kyc15ramv8
slug: ansible-playbooks-writing-multiple-tasks-and-plays-in-a-single-playbook
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1757902572260/cfe5c0c7-8ea0-43d0-83aa-2600e4938f6a.png

---

Hey everyone, hope you had a great weekend! 🌟  
I’m starting my day with writing this blog, and before we dive in, here’s a quick recap of what we’ve covered so far:

✅ **Blogs Completed**

1️⃣ **Shell Scripting vs Ansible: Which One Should You Use?**

* Compared Shell scripting and Ansible.
    
* Highlighted time, coding skills, and maintenance challenges with Shell.
    
* Showed how Ansible is simple, powerful, and agentless.
    
* Gave practical perspective on when to use which.
    

2️⃣ **Why Ansible Dominates Other Configuration Tools**

* Compared Ansible with Puppet, Chef, and SaltStack.
    
* Explained simplicity, agentless design, YAML playbooks, idempotency, and faster adoption.
    
* Real-world scenarios where Ansible outshines others.
    

3️⃣ **Getting Started with Ansible: Installation, Control Node Communication, and First Ad-hoc Command**

* Installing Ansible.
    
* How the Control Node communicates with Managed Nodes (via SSH, no agents).
    
* Ran the very first ad-hoc command.
    

4️⃣ **Mastering Ansible Ad-hoc Commands: Understanding Modules and Practical Examples**

* Deep dive into ad-hoc commands.
    
* Covered modules: ping, command, shell, file, copy, lineinfile, package, service, systemd, debug, user.
    
* Gave practical examples: creating files, directories, managing packages, checking services.
    
* Explained difference between shell vs command module.
    

5️⃣ **Getting Started with Ansible Playbooks: What, Why, and Writing Your First One**

* Why we need playbooks vs ad-hoc commands.
    
* What a playbook is (YAML, tasks, sequential execution, idempotency).
    
* First playbook example → ping servers.
    
* Refreshed inventory file concepts (groups, children).
    
* Executed the playbook using `ansible-playbook`.
    

## 👉 **Today’s Topic: Writing Our 2nd Playbook + Multiple Plays in a Single Playbook**

In the previous blog, we wrote our very first playbook. Now let’s take it one step further.

Suppose you want to install **nginx** (or httpd) on your Linux servers and then **start and enable the service**.

On **RHEL**, you’d normally run:

```bash
dnf install nginx -y  
systemctl start nginx  
systemctl enable nginx  
```

On **Ubuntu/Debian**, you’d run:

```bash
apt-get update  
apt-get install nginx -y  
systemctl start nginx  
systemctl enable nginx  
```

We already discussed in our Ansible vs Shell blog how Ansible makes this simple, while Shell scripts handle it in a more complex way.  
Today, let’s explore how to achieve this the simple **Ansible** way — with a **playbook**.

### ✅ Playbook with Multiple Tasks

```yaml
- name: install and run nginx
  hosts: web
  become: yes
  tasks:
    - name: install nginx
      ansible.builtin.package:
        name: nginx
        state: present

    - name: start and enable nginx
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: yes
```

👉 This playbook will:

* Install nginx
    
* Start it
    
* Enable it on boot
    

All in one go — reusable and idempotent.

### ✅ Playbook with Multiple Plays

```yaml
- name: PLAY1
  hosts: local
  connection: local  # managing its own node, no credentials needed
  tasks:
    - name: PLAY1 and TASK1
      ansible.builtin.debug:
        msg: "Hello from PLAY1 and TASK1"

- name: PLAY2
  hosts: local
  connection: local
  tasks:
    - name: PLAY2 and TASK1
      ansible.builtin.debug:
        msg: "Hello from PLAY2 and TASK1"
```

👉 Notice:

* With `connection: local`, the Control Node manages itself (no credentials needed).
    
* You can define **multiple plays** inside one playbook.
    
* Each play can have its own hosts, tasks, and behavior.
    

In this case, we’re just saying “Hello” from different plays — but in real-world automation, you’d use this approach to separate tasks by roles (e.g., **web servers vs database servers**).

🎯 That’s all for today!  
In the next blog, we’ll dive into **Variables in Ansible Playbooks** — making them even more flexible and powerful.

### ✅ **Conclusion**

With today’s blog, we learned how Ansible Playbooks make automation simple and reliable:

* You can run **multiple tasks** in a single playbook (like installing and starting Nginx).
    
* You can also define **multiple plays** in the same playbook, each targeting different hosts or purposes.
    
* Using `connection: local`, you can even manage the control node itself without extra credentials.
    
* Playbooks stay **clean, readable, and idempotent**, ensuring repeat runs don’t break your system.
    

This structured approach is what makes Ansible far more powerful than writing manual commands or scripts. 🚀

Up next: we’ll dive into **variables in Ansible Playbooks** to make automation more dynamic and reusable.