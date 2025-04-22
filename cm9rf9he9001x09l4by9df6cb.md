---
title: "Chapter 18 : Linux OS Hardening – Practical Steps to Secure and Fortify Your System"
seoTitle: "Linux OS Hardening: Secure Your System"
seoDescription: "Learn practical steps to harden and secure your Linux operating system, ensuring robust protection against vulnerabilities and attacks"
datePublished: Mon Apr 21 2025 18:41:14 GMT+0000 (Coordinated Universal Time)
cuid: cm9rf9he9001x09l4by9df6cb
slug: linux-os-hardening-practical-steps-to-secure-and-fortify-your-system
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745323692037/ccf6b962-91cb-4618-903b-46127cd9566c.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1745323703372/704f8dad-5248-423f-910d-3e19e00f5e2f.jpeg

---

## 🔐 **1\. Linux OS Hardening – Strengthening Your Linux Fortress**

**Introduction:** Linux OS hardening is a crucial step in securing a system by reducing its attack surface. The goal is to minimize vulnerabilities by securing user accounts, services, ports, files, and configurations.

---

### 🔸 **1\. User Account Management**

**Goal:** Ensure that only authorized users have access and unnecessary accounts are removed.

**Best Practices:**

* Disable or delete default and unused user accounts.
    
* Enforce password policies (expiry, complexity, history).
    
* Use `passwd`, `chage`, `usermod`, and `faillock` commands.
    

**Example:**

```bash
# Lock a user account
usermod -L testuser

# Expire password after 90 days
chage -M 90 testuser

# Check password aging policy
chage -l testuser
```

**Real-World Use Case:** In a production environment, locking accounts of former employees is vital. Auditing user activity using `/var/log/secure` or `auditd` helps maintain accountability.

---

### 🔸 **2\. Remove Unwanted Packages**

**Goal:** Remove software that is not in use to minimize vulnerabilities.

**Commands:**

```bash
# List installed packages
rpm -qa

# Remove a package
yum remove telnet -y
```

**Real-World Use Case:** Tools like `telnet`, `ftp`, or old compilers can introduce risks. Removing them reduces attack vectors.

---

### 🔸 **3\. Disable Unused Services**

**Goal:** Prevent unnecessary daemons from running.

**Commands:**

```bash
# List active services
systemctl list-units --type=service

# Stop and disable unused service
systemctl stop bluetooth
systemctl disable bluetooth
```

**Real-World Use Case:** On a server where Bluetooth or CUPS is unnecessary, disabling them prevents resource consumption and potential exploits.

---

### 🔸 **4\. Check for Listening Ports**

**Goal:** Identify open ports and associated services.

**Commands:**

```bash
netstat -tuln
ss -tuln
```

**Real-World Use Case:** A new port opened by mistake can expose your server. Regular checks catch misconfigurations early.

---

### 🔸 **5\. Secure SSH Configuration**

**Goal:** Harden SSH access.

✅ Disable root login  
✅ Change default port  
✅ Set idle timeout  
✅ Disable empty passwords  
✅ Allow specific users  
✅ Use SSH keys

We'll cover this deeply in the SSH section.

---

### 🔸 **6\. Enable Firewall (iptables / firewalld)**

**Goal:** Control incoming and outgoing traffic.

**Commands:**

```bash
# For firewalld
systemctl start firewalld
firewall-cmd --permanent --add-port=22/tcp
firewall-cmd --reload
```

**Real-World Use Case:** Limiting access to specific ports (e.g., allow 80, 443 only) on web servers is standard hardening practice.

---

### 🔸 **7\. Enable SELinux**

**Goal:** Add a layer of access control security.

**Commands:**

```bash
# Check status
getenforce

# Enable SELinux
setenforce 1
```

Modify `/etc/selinux/config` for permanent enforcement:

```bash
SELINUX=enforcing
```

**Real-World Use Case:** SELinux confines processes and files, preventing attackers from moving laterally if they compromise a process.

---

### 🔸 **8\. Change Default Listening Ports**

**Goal:** Reduce brute-force and scanning attacks.

**Example (SSH):**

```bash
# Change port in /etc/ssh/sshd_config
Port 2222
systemctl restart sshd
```

**Real-World Use Case:** Changing SSH to a non-standard port (e.g., 2022) drastically reduces automated login attempts.

---

### 🔸 **9\. Keep OS Up-to-Date**

**Goal:** Patch security vulnerabilities regularly.

**Commands:**

```bash
yum update -y
dnf upgrade --security
```

**Real-World Use Case:** Most exploits target unpatched systems. Automated patching tools like `yum-cron` or `dnf-automatic` help maintain security posture.

---

## 🗂️ **2\. OpenLDAP Installation & Configuration in Linux**

**Introduction:** OpenLDAP (Open Lightweight Directory Access Protocol) is an open-source implementation of the LDAP protocol, widely used for centralized authentication, directory services, and access control in enterprise environments.

It provides a structured directory tree to store information about users, systems, services, and more. Think of it as a centralized phonebook — one place to manage authentication across many systems.

---

### 🔸 **What is OpenLDAP?**

* LDAP is a protocol used to access and maintain distributed directory information services over an IP network.
    
* OpenLDAP is its open-source version, commonly used in Linux environments.
    
* It’s ideal for managing user information, host details, or even application config data in a hierarchical and searchable structure.
    

**Real-World Use Case:** Imagine a company with 200+ Linux servers and multiple teams. Instead of managing user accounts individually, OpenLDAP provides a central place to authenticate and authorize access.

---

### 🔸 **OpenLDAP Service Components**

* **slapd**: The main LDAP daemon/service.
    
* **slurpd**: (Deprecated) used for LDAP replication (slapd now handles this).
    
* **ldapadd/ldapmodify/ldapsearch**: CLI tools to interact with the LDAP server.
    

---

### 🔸 **Starting & Enabling slapd Service**

```bash
# Install OpenLDAP and client utilities (on RHEL/CentOS/Rocky)
yum install openldap-servers openldap-clients -y

# Start slapd (the LDAP daemon)
systemctl start slapd

# Enable it at boot
systemctl enable slapd

# Check status
systemctl status slapd
```

**Real-World Tip:** On production systems, ensure slapd is always running with monitoring (Nagios, Zabbix) and alerting configured.

---

### 🔸 **Important Configuration Directories & Files**

| File/Directory | Purpose |
| --- | --- |
| `/etc/openldap/` | Base directory for LDAP configuration |
| `/etc/openldap/slapd.d/` | Main slapd runtime configuration (dynamic, unlike slapd.conf) |
| `/var/lib/ldap/` | Directory for the actual LDAP database |
| `/etc/openldap/ldap.conf` | Global LDAP client configuration file |
| `/etc/openldap/certs/` | Store TLS/SSL certificates here |

**Note:** `slapd.d/` is the preferred config method (LDIF format) in newer distributions. Avoid editing it manually — use `ldapmodify`.

---

### 🔸 **Example: Adding a User to OpenLDAP**

Step-by-step on how you might add a user via LDIF file:

**Step 1: Create a user LDIF file**

```bash
dn: uid=john,ou=People,dc=durgarevu,dc=com
objectClass: inetOrgPerson
cn: John Doe
sn: Doe
uid: john
userPassword: john123
```

**Step 2: Add the user**

```bash
ldapadd -x -D "cn=admin,dc=durgarevu,dc=com" -W -f john.ldif
```

**Real-World Use Case:** Integrating OpenLDAP with SSH and PAM allows centralized login and authorization. You can even connect it to Samba for file sharing or integrate with FreeIPA.

---

### 🔸 **Securing OpenLDAP with TLS**

To encrypt communication between client and server:

1. Generate or obtain SSL certificates.
    
2. Modify slapd configuration to enable TLS.
    
3. Configure `/etc/openldap/ldap.conf` with:
    

```bash
TLS_CACERT /etc/openldap/certs/ca.crt
```

**Command to test TLS:**

```bash
ldapsearch -x -H ldaps://ldapserver.durgarevu.com -b "dc=durgarevu,dc=com"
```

**Real-World Tip:** Never deploy LDAP in production without TLS encryption — sensitive user credentials are transmitted.

---

## 🌐 **3\. Trace Network Traffic with** `traceroute` in Linux

**Introduction:**  
Network issues can be difficult to pinpoint—sometimes it’s not the destination server that’s the problem, but something along the way. That’s where `traceroute` comes in. It maps the journey of a packet from your machine to a remote host and helps you identify exactly **where** delays or packet drops occur.

---

### 🔸 **What is** `traceroute`?

* `traceroute` is a **network diagnostic tool** used to track the path that a packet takes across an IP network.
    
* It shows **each hop** (router or server) a packet passes through and **how long** it takes to get there.
    
* Very useful in **troubleshooting network slowness, black holes, or downed services**.
    

---

### 🔧 **Basic Syntax**

```bash
traceroute [destination_host]
```

Example:

```bash
traceroute www.google.com
```

---

### 📊 **Sample Output Explanation**

```bash
traceroute to www.google.com (142.250.195.100), 30 hops max, 60 byte packets
 1  192.168.0.1 (192.168.0.1)  1.234 ms  0.983 ms  0.789 ms
 2  10.0.0.1 (10.0.0.1)  12.134 ms  13.098 ms  12.878 ms
 3  172.217.0.10 (172.217.0.10)  25.643 ms  24.987 ms  25.001 ms
...
```

| Column | Description |
| --- | --- |
| Hop # | Number of the hop (each router it passes through) |
| IP / Hostname | IP address or name of the device at that hop |
| Time (ms) | Round trip time from your system to the hop (3 attempts shown) |

---

### 🔎 **Use Cases**

#### ✅ **1\. Identify Network Latency**

If one hop has significantly higher response times than others, that may be a network bottleneck.

#### ✅ **2\. Detect Packet Loss**

If some hops don’t respond (`* * *`), there may be a firewall or a dropped route.

#### ✅ **3\. Validate Route Taken by Data**

Helps verify if the packet is taking an unexpected or suboptimal route, especially in cloud or multi-region setups.

---

### ⚙️ **Traceroute Over UDP, ICMP, and TCP**

* **Default**: Uses UDP packets.
    
* **ICMP**: For firewalls that block UDP, use `-I` to switch to ICMP (like Windows `tracert`).
    
    ```bash
    traceroute -I www.google.com
    ```
    
* **TCP**: Useful to trace through more firewalls.
    
    ```bash
    traceroute -T www.google.com
    ```
    

---

### 📘 **Real-World Example**

> **Scenario**: A system administrator is investigating slow response time when accessing an internal app hosted in AWS from the corporate office.

1. Runs `traceroute` [`app.internal.aws.com`](http://app.internal.aws.com)
    
2. Finds the delay starts at hop #7, which is the office firewall gateway.
    
3. Coordinates with the network team to review firewall logs and resolve the bottleneck.
    

---

### 🛠️ **Pro Tip**

* Combine with `ping` to verify end-to-end packet drops:
    
    ```bash
    ping www.google.com
    ```
    
* Use `mtr` (modern traceroute + ping):
    
    ```bash
    mtr www.google.com
    ```
    

---

## 🔐 **4\. Configure and Secure SSH – Best Practices for System Administrators**

**Overview:**  
SSH (Secure Shell) is the backbone of remote system administration. While it provides encrypted communication, it’s also a frequent target of attackers. Securing SSH is critical to hardening your Linux server.

---

### 📘 **What is SSH?**

* **SSH (Secure Shell)** is a **network protocol** that enables secure remote access over an unsecured network.
    
* Used for:
    
    * Administering remote Linux servers
        
    * Secure file transfers (via `scp` or `sftp`)
        
    * Remote tunneling and automation
        

---

### 🧱 **OpenSSH Service Overview**

* **Software package**: `openssh`
    
* **Service name**: `sshd`
    
* **Default port**: `22`
    
* **Start/Stop/Enable**:
    
    ```bash
    systemctl start sshd
    systemctl enable sshd
    systemctl status sshd
    ```
    

---

## 🔒 **Essential SSH Hardening Techniques**

---

### ✅ 1. **Configure Idle Timeout**

Prevent abandoned sessions from being misused.

**Steps:**

```bash
sudo nano /etc/ssh/sshd_config

# Add or update these:
ClientAliveInterval 600
ClientAliveCountMax 0
```

Restart the service:

```bash
systemctl restart sshd
```

**Result**: Logs out idle users after 10 minutes.

---

### ✅ 2. **Disable Root Login**

Prevent direct `root` logins to stop brute-force or accidental misuse.

**Steps:**

```bash
sudo nano /etc/ssh/sshd_config

# Change this:
PermitRootLogin no

systemctl restart sshd
```

**Best Practice**: Use a regular user + `sudo`.

---

### ✅ 3. **Disable Empty Passwords**

Avoid allowing users with blank passwords.

```bash
sudo nano /etc/ssh/sshd_config

# Ensure this line is present and not commented:
PermitEmptyPasswords no

systemctl restart sshd
```

---

### ✅ 4. **Limit SSH Access to Specific Users**

Only allow certain users to log in remotely.

```bash
sudo nano /etc/ssh/sshd_config

# Add your allowed users:
AllowUsers revanth devopsadmin
```

Restart SSH service:

```bash
systemctl restart sshd
```

---

### ✅ 5. **Change SSH Port**

Changing the default port can reduce exposure to bots scanning port 22.

```bash
sudo nano /etc/ssh/sshd_config

# Replace with your chosen port:
Port 2222

# Restart and open firewall port:
systemctl restart sshd
firewall-cmd --add-port=2222/tcp --permanent
firewall-cmd --reload
```

---

## 🔐 **SSH Key-Based Authentication – Passwordless & Secure**

Used for:

* Automation (scripts, cron jobs)
    
* Frequent logins between hosts
    

---

### 🧪 **Steps to Set Up SSH Key Authentication**

**On the Client (e.g., MyFirstLinuxVM):**

#### Step 1 — Generate Key Pair

```bash
ssh-keygen
```

* Creates `~/.ssh/id_rsa` (private) and `id_`[`rsa.pub`](http://rsa.pub) (public)
    

#### Step 2 — Copy Public Key to Server

```bash
ssh-copy-id root@192.168.1.x
```

#### Step 3 — Login Without Password

```bash
ssh root@192.168.1.x
# or
ssh -l root 192.168.1.x
```

---

### 🎯 **Real-World Use Case:**

> **Scenario**: You're automating backup scripts from a Linux VM to a remote backup server.

1. Configure SSH keys between the two systems.
    
2. Use `scp` or `rsync` in cronjobs to push files.
    
3. No password needed. Seamless and secure automation.
    

---

### 🧠 Bonus Tip – Audit SSH Logs

Monitor SSH login attempts and access:

```bash
journalctl -u sshd
cat /var/log/secure  # RHEL/CentOS
```

## 🔚 **Conclusion: Secure Today, Sleep Better Tomorrow**

Hardening a Linux operating system isn’t just a one-time task—it’s an ongoing process that plays a vital role in protecting your infrastructure from evolving threats. By following the best practices we’ve covered—such as minimizing user privileges, securing SSH, removing unused services and packages, updating regularly, and leveraging tools like firewalld and SELinux—you lay a solid foundation for a secure and resilient system.

Remember, even the smallest oversight—like leaving a service unnecessarily active or allowing root login over SSH—can open the door to serious breaches. Automation, auditing, and regular reviews are your friends in the long run.

Security doesn’t come from tools alone—it comes from discipline, awareness, and a proactive mindset.

✅ Harden.  
✅ Monitor.  
✅ Update.  
✅ Repeat.

Your Linux systems deserve nothing less.