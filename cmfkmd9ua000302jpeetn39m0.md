---
title: "Getting Started with Ansible Variables: Definition, Usage, and Precedence"
seoTitle: "Ansible Variables: Basics and Key Concepts"
datePublished: Mon Sep 15 2025 04:23:59 GMT+0000 (Coordinated Universal Time)
cuid: cmfkmd9ua000302jpeetn39m0
slug: getting-started-with-ansible-variables-definition-usage-and-precedence
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1757910202482/1ebb179c-f566-4c1f-bb12-34a20d44213d.png

---

Hey everyone! 👋  
So far, we’ve covered **Ad-hoc commands**, **Playbooks**, and writing multiple tasks and plays. Today, we’re taking one step further — **Variables in Ansible**!

Variables make your playbooks **dynamic, reusable, and easy to maintain**. They are one of the most important concepts in Ansible, and in this blog, we’ll also discuss **variable precedence** — which determines which variable value takes effect when multiple variables with the same name exist.

## ✅ What Are Variables in Ansible?

Just like in programming or shell scripting, variables are placeholders for values that may change depending on the **host, environment, or user input**.

* They help **avoid hardcoding values** in tasks.
    
* They make automation **flexible** across multiple hosts.
    
* They make playbooks **readable and easier to maintain**.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757909565296/afab4905-c969-496d-8dfc-9580e13e52d3.png align="center")

### How to Define and Use Variables

In **shell scripting**, we define a variable like:

```bash
COURSE=Ansible
echo $COURSE
```

In **Ansible**, we use **Jinja2 templating** syntax:

```yaml
"{{ COURSE }}"
```

### Example: Play-Level Variables

```yaml
- name: Using variables
  hosts: local
  connection: local
  vars: # Play-level variables
    COURSE: "Ansible with Linux"
    DURATION: 10HRS
  tasks:
    - name: Print the variables
      ansible.builtin.debug:
        msg: "Course is {{ COURSE }}, Duration is {{ DURATION }}"
```

Here, `COURSE` and `DURATION` are variables. You can **change their values in one place**, and it will reflect everywhere they are used. This follows the **DRY principle** (Don’t Repeat Yourself).

## ⚡ Variable Precedence

Sometimes, variables with the same name exist in multiple places. **Ansible decides which value to use based on precedence**.

Here’s the **most commonly used order of precedence**:

1. **Command line / Extra vars** (`-e`)
    
2. **Task-level variables**
    
3. **Vars files** (`vars_files`)
    
4. **Prompt** (`vars_prompt`)
    
5. **Play-level variables** (`vars`)
    
6. **Inventory variables** (`inventory.ini`)
    
7. **Roles** (we’ll discuss roles in future blogs)
    

### Example: Task-Level Overrides Play-Level

```yaml
- name: Variables precedence example
  hosts: local
  connection: local
  vars:  # Play-level variables
    COURSE: "Ansible"
    DURATION: 10HRS
  tasks:
    - name: Task-level variables override play-level
      vars:  # Task-level variable
        COURSE: "Linux"
      ansible.builtin.debug:
        msg: "Course is {{ COURSE }}, Duration is {{ DURATION }}"

    - name: No task-level override
      ansible.builtin.debug:
        msg: "Course is {{ COURSE }}, Duration is {{ DURATION }}"
```

**Output:**

```bash
Task 1: Course is Linux, Duration is 10HRS
Task 2: Course is Ansible, Duration is 10HRS
```

**Explanation:**

* First task: Task-level `COURSE: Linux` overrides play-level `COURSE: Ansible`.
    
* Second task: No task-level variable, so play-level variable is used.
    

### Example: Variables from Files

`course.yaml`:

```yaml
COURSE: "Ansible from Files"
DURATION: 10HRS
GREET: "From files"
```

```yaml
- name: Variables from file
  hosts: local
  connection: local
  vars_files:
    - course.yaml
  tasks:
    - name: Print course information
      ansible.builtin.debug:
        msg: "Course is {{ COURSE }}, Duration is {{ DURATION }} , GREET: {{ GREET }}"
```

**Output:**

```bash
Course is Ansible from Files, Duration is 10HRS, GREET: From files
```

### **Inventory Variables**

You can also define variables per host in your inventory:

`inventory.ini`

```bash
[web]
172.31.93.128 COURSE="Ansible from Inventory" DURATION="10HRS"
```

Inventory variables are **lower precedence than task-level, vars\_files, or vars\_prompt**.

### Example: Variables from Prompt

You can ask the user to provide variable values at runtime:

```yaml
- name: Variables from prompt
  hosts: local
  connection: local
  vars_prompt:
    - name: COURSE
      prompt: "Please enter course name"
      private: false
    - name: DURATION
      prompt: "Please enter duration"
      private: false
  tasks:
    - name: Print course information
      ansible.builtin.debug:
        msg: "Course is {{ COURSE }}, Duration is {{ DURATION }}"
```

**Explanation:**

* Prompted variables **override play-level and file variables**.
    
* `private: false` ensures the input is visible while typing.
    

### Example: Command Line / Extra Vars

You can override any variable directly when running your playbook using the `-e` flag. This has the **highest precedence**.

```bash
ansible-playbook -i inventory.ini playbook.yaml -e "COURSE='Ansible CLI' DURATION='5HRS'"
```

In your `playbook.yaml`:

```yaml
- name: CLI variable example
  hosts: local
  connection: local
  vars:  # Play-level vars (will be overridden by CLI)
    COURSE: "Ansible"
    DURATION: 10HRS
  tasks:
    - name: Print variables
      ansible.builtin.debug:
        msg: "Course is {{ COURSE }}, Duration is {{ DURATION }}"
```

**Output:**

```bash
TASK [Print variables] *******************************************************
ok: [localhost] => {
    "msg": "Course is Ansible CLI, Duration is 5HRS"
}
```

✅ As you can see, the values passed via **command line** override the play-level values.

### Combining Variables

You can also combine **play-level vars, vars\_files, vars\_prompt, and task-level vars** in one playbook. The **highest-precedence value wins** based on the order we discussed.

```yaml
- name: Combined variable example
  hosts: local
  connection: local
  vars: # Play-level
    GREET: "PLAY"
  vars_files:
    - course.yaml
  vars_prompt:
    - name: GREET
      prompt: "Enter greeting"
      private: false
  tasks:
    - name: Task-level variable
      vars:
        GREET: "TASK"
      ansible.builtin.debug:
        msg: "Hello from {{ GREET }}"
```

**Explanation:**

* **Command line or args** `GREET` &gt; **Task-level** `GREET` &gt; **File** `GREET` &gt; **Prompt** `GREET` &gt; **Play-level** `GREET` &gt; **Inventory** `GREET`
    

### **Summary of Variable Precedence**

| **Precedence Level** | **Description** |
| --- | --- |
| 1\. Command line / extra-vars | Highest priority; overrides all others |
| 2\. Task-level variables | Defined in `vars` inside a task |
| 3\. Files (`vars_files`) | Variables loaded from external YAML files |
| 4\. Prompt (`vars_prompt`) | User input at runtime |
| 5\. Play-level variables | Defined in `vars` at the play level |
| 6\. Inventory variables | Host-specific variables in inventory |
| 7\. Roles (defaults) | Default values set in role, lowest priority |

> Always remember: **higher precedence variable overrides lower precedence ones.**

### ✅ Key Takeaways

* Variables make your playbooks **dynamic, reusable, and maintainable**.
    
* Variable precedence ensures **which value takes effect** when multiple definitions exist.
    
* You can define variables in **playbooks, files, prompts, inventory, tasks, command line, and roles**.
    
* Mastering variables is **essential for advanced Ansible automation**.
    

### ✅ **Conclusion**

Variables are the backbone of **dynamic and reusable Ansible playbooks**.

By using variables, you can:

* Avoid hardcoding values in tasks ✅
    
* Make playbooks flexible across multiple hosts and environments ✅
    
* Keep your automation **DRY** and easy to maintain ✅
    
* Control which variable takes effect using **variable precedence** ✅
    

Remember: the **highest precedence** wins — command line &gt; task &gt; files &gt; prompt &gt; play &gt; inventory &gt; roles.

Mastering variables sets the foundation for **writing scalable, readable, and efficient automation**, and prepares you for more advanced concepts like **Roles, Templates, and Conditionals**. 🚀