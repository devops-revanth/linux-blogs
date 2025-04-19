---
title: "Chapter 15 : Understanding Linux Networking – Components, Interfaces, and Tools"
seoTitle: "Linux Networking Essentials: Components & Tools"
seoDescription: "Explore Linux networking essentials: components, configurations, tools like nmtui and nmcli, and essentials for SysAdmins and cloud professionals"
datePublished: Sat Apr 19 2025 14:59:36 GMT+0000 (Coordinated Universal Time)
cuid: cm9ocgqzx000309l4h0ro4kon
slug: understanding-linux-networking-components-interfaces-and-tools

---

Networking is the backbone of any server or system in real-world IT environments. Whether it’s connecting to the internet, internal servers, or configuring network interfaces for load balancing and redundancy, mastering Linux networking fundamentals is critical for SysAdmins, DevOps, and cloud professionals.

In this chapter, we will explore:

1. Core Network Components – IP, Subnet Mask, Gateway, DHCP vs Static IPs
    
2. Essential Network Files and Commands
    
3. NIC (Network Interface Card) Configuration & Bonding
    
4. Interactive Tools – `nmtui`, `nmcli`, and `nm-connection-editor`
    

---

## 🔌 **Network Components in Linux**

---

#### 🧩 **IP Address**

An IP address is a unique identifier assigned to a system on a network. It enables communication between devices.

**Command to view IP:**

```bash
ip addr show
```

Or

```bash
ip a
```

**Example:**

```bash
inet 192.168.1.10/24 brd 192.168.1.255 scope global dynamic noprefixroute enp0s3
```

**Use Case:**  
Assigning IP addresses for internal networks in offices, VMs in cloud servers, or setting up DNS servers.

---

#### 🧩 **Subnet Mask**

Determines the range of IP addresses within a network.

**Example:** A subnet mask of `255.255.255.0` allows 254 usable IPs (e.g., 192.168.1.1 to 192.168.1.254).

**Use Case:**  
Helps divide networks for better organization and security. For example, separate HR and Finance on different subnets.

---

#### 🧩 **Default Gateway**

The gateway is used to route traffic from your local network to other networks or the internet.

**Command:**

```bash
ip route show
```

**Example Output:**

```bash
default via 192.168.1.1 dev enp0s3 proto dhcp metric 100 
```

**Use Case:**  
Default gateways are essential for internet connectivity in cloud VMs, local desktops, or IoT devices.

---

#### 🧩 **Static vs DHCP**

* **Static IP**: Manually assigned, doesn't change.
    
* **DHCP (Dynamic Host Configuration Protocol)**: Automatically assigned by a DHCP server.
    

**Config Files:**

* Static: You define IP, subnet, and gateway.
    
* DHCP: System requests IP from DHCP server.
    

**Use Case:**

* Use **Static IP** for servers, printers, routers.
    
* Use **DHCP** for clients, laptops, dynamic environments.
    

**Example in** `/etc/sysconfig/network-scripts/ifcfg-enp0s3`:

```bash
BOOTPROTO=static
IPADDR=192.168.1.10
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
```

---

#### 🧩 **Interface**

Network interface is the hardware (or virtual) component that connects your system to a network.

**Command to view:**

```bash
ip link show
```

**Example Output:**

```bash
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP>
```

**Use Case:**  
Identify active NICs, debug connectivity issues, enable/disable

---

## 🔄 Evolution of Network Configuration in RHEL: From Legacy to Modern

---

### 🧭 1. **Legacy Networking in RHEL 6 and Below**

In RHEL 6 (and earlier), networking was managed using **traditional init scripts** and static configuration files.

#### 📁 Configuration Files (Old Method):

* `/etc/sysconfig/network`
    
* `/etc/sysconfig/network-scripts/ifcfg-*` (one per interface)
    
* `/etc/resolv.conf` for DNS
    
* `/etc/hosts`, `/etc/hostname`
    

#### 🧰 Tools:

* `ifconfig`
    
* `service network restart` or `/etc/init.d/network restart`
    

---

### ⚠️ Problems with the Legacy Method:

* Manual edits to flat files (prone to syntax errors)
    
* Restarting network service could break connectivity
    
* Limited ability to manage dynamic or complex setups (e.g. WiFi, VPN)
    
* Poor support for mobile/virtual/cloud setups
    

---

### 🚀 2. **Modern Networking: RHEL 7, 8, and 9**

With RHEL 7 onward, **NetworkManager** became the default backend for managing network interfaces.

#### 🔄 What Changed?

| Feature/Component | RHEL 6 and Below | RHEL 7, 8, 9 |
| --- | --- | --- |
| Network backend | init scripts | `NetworkManager` |
| Interface config files | `/etc/sysconfig/network-scripts/ifcfg-*` | *Still supported (for compatibility), but discouraged* |
| Commands/tools | `ifconfig`, `service network` | `nmcli`, `nmtui`, `systemctl` |
| Service name | `network` | `NetworkManager` |
| DNS management | Manual via `/etc/resolv.conf` | Dynamically managed by NM |

---

#### 🔧 Why the Change?

* **Dynamic Networking:** Needed for cloud, containers, virtual machines, mobile devices.
    
* **Better Tools:** `nmcli` and `nmtui` offer CLI and TUI interfaces for automation and interactive config.
    
* **Integration:** Works well with GUI, CLI, systemd, VPNs, and WiFi tools.
    
* **Safety:** No more risky network restarts that could cut you off remotely.
    

---

### ✅ What Still Works?

Even in RHEL 9, you **can still use** ifcfg-\* files and even the old `/etc/resolv.conf`, but they are no longer the preferred method.

* **Backward Compatibility:** RHEL preserves these to support legacy scripts and systems.
    
* **Best Practice:** Use `nmcli`, `nmtui`, or direct NetworkManager D-Bus APIs for modern management.
    

---

### 🔍 Example: Static IP in Legacy vs Modern

#### Legacy Method (RHEL 6):

```ini
# /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
BOOTPROTO=static
IPADDR=192.168.1.50
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
ONBOOT=yes
```

Restart with:

```bash
service network restart
```

---

#### Modern Method (RHEL 7+ with NetworkManager):

Using `nmcli`:

```bash
nmcli con add type ethernet ifname eth0 con-name static-eth0 ip4 192.168.1.50/24 gw4 192.168.1.1
nmcli con mod static-eth0 ipv4.dns "8.8.8.8"
nmcli con up static-eth0
```

Or edit via TUI:

```bash
nmtui
```

**No need to restart the entire network — only the affected connection.**

---

### 💡 Real-Time Use Cases:

* **DevOps Pipelines:** Automate interface configuration using `nmcli` in Kickstart or Ansible.
    
* **Cloud VMs:** Add secondary IPs without disrupting the main connection.
    
* **Resilient Servers:** Avoid full restarts that can lock you out from SSH.
    

---

## 📁 **Network Files and Commands – Deep Dive**

Linux stores networking configuration in various files and provides powerful commands to manage and troubleshoot networks. Let's explore the most essential ones.

---

#### 📄 **Important Network Configuration Files**

---

##### 🔹 `/etc/sysconfig/network-scripts/ifcfg-*`

(Used in RHEL/CentOS-based systems)

These files define settings for individual network interfaces.

**Example:**

```ini
DEVICE=enp0s3
BOOTPROTO=static
IPADDR=192.168.1.10
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
ONBOOT=yes
```

**Use Case:**  
Modify these files to permanently assign a static IP address to a network interface.

---

##### 🔹 `/etc/resolv.conf`

This file configures DNS servers.

**Example:**

```bash
nameserver 8.8.8.8
nameserver 1.1.1.1
```

**Use Case:**  
Set DNS resolution for your system to external/public DNS servers like Google or Cloudflare.

---

##### 🔹 `/etc/hosts`

Used for local name resolution. Maps hostnames to IPs without querying DNS.

**Example:**

```bash
127.0.0.1   localhost
192.168.1.10   myserver.localdomain myserver
```

**Use Case:**  
Testing applications with hostname mapping or in offline environments.

---

##### 🔹 `/etc/hostname`

Defines the system hostname.

**Example:**

```bash
webserver01
```

**Command to check or change:**

```bash
hostnamectl set-hostname newname
```

---

#### 🛠️ **Key Network Commands**

---

##### 🔧 `ip a` or `ip addr`

Shows IP addresses assigned to all interfaces.

##### 🔧 `ip r` or `ip route`

Displays routing table.

##### 🔧 `nmcli dev show`

Displays network configuration per interface.

##### 🔧 `ping`, `traceroute`, `dig`, `nslookup`

Used for network troubleshooting and DNS testing.

---

#### 🔍 Real-World Use Cases

* Modify `/etc/resolv.conf` to resolve DNS issues in cloud servers.
    
* Use `/etc/hosts` for local testing of web applications.
    
* Add static entries in ifcfg files during server provisioning with tools like Kickstart or Ansible.
    

---

## 🧠 NIC (Network Interface Card) Information in Linux

In Linux, identifying and understanding NIC details is crucial for network troubleshooting, interface configuration, and performance tuning.

---

### 🧾 RHEL 6 and Earlier – Legacy Methods

#### 🔹 Tools Used:

* `ifconfig` – Display all NICs and IPs.
    
* `ethtool` – Get NIC driver, speed, duplex info.
    
* `/etc/sysconfig/network-scripts/ifcfg-*` – Interface config files.
    

#### 🔍 Example: View NIC Details with `ifconfig`

```bash
ifconfig eth0
```

**Output shows**: IP address, MAC, RX/TX packets, MTU, etc.

#### 📌 Real Use Case:

You’re debugging a server that can't ping the gateway. You use `ifconfig` to check if `eth0` has an IP assigned. If not, you inspect `/etc/sysconfig/network-scripts/ifcfg-eth0`.

#### 🔧 Tool: `ethtool`

```bash
ethtool eth0
```

Shows speed, duplex, and driver details. Useful when checking link-level issues.

---

### 🚀 RHEL 7/8/9 – Modern Methods (Post systemd, NetworkManager era)

#### 🔹 Tools Replaced:

* `ifconfig` → Replaced by `ip` from `iproute2`.
    
* `/etc/sysconfig/` configs deprecated → Now managed by NetworkManager.
    
* `nmcli`, `nmtui`, and systemd config files are now preferred.
    

#### 📌 View NICs with `ip`

```bash
ip addr show
ip link show
```

#### 🔍 Example: Identify all interfaces

```bash
ip a
```

Shows loopback, enp0s3 (or eno1, ens33 depending on naming), with IPs and states.

#### 📄 Get NIC details with `nmcli`

```bash
nmcli device show enp0s3
```

Displays all information like IP, MAC, DNS, Gateway, driver, etc.

#### 🧰 Real Use Case:

If a server has multiple NICs, and you're configuring routing rules or bonding, `nmcli` gives you a reliable way to script and manage NIC state/configuration.

---

### 🧵 NIC Naming Evolution – Predictable Naming

#### ❌ Old:

RHEL 6 used names like `eth0`, `eth1`, etc. Not stable across reboots or hardware changes.

#### ✅ New:

RHEL 7+ introduced **Predictable Network Interface Names**, e.g., `enp0s3`, `ens33`, `eno1`.

Why? To prevent NIC name flipping after hardware changes, causing network misconfigurations.

---

### ✅ Real-World Troubleshooting Scenarios

| Scenario | Old Method | New Method |
| --- | --- | --- |
| No IP on boot | `ifconfig`, inspect `ifcfg-*` | `nmcli device show`, check connection profiles |
| Link down issues | `ethtool eth0` | `ethtool enp0s3` or `nmcli` |
| NIC bonding | `modprobe`, `ifcfg-bond*` | `nmcli con add type bond...` |
| Scripted IP setup | Edit config files | Use `nmcli` or `nmcli connection` |

---

## 🔗 NIC Bonding in Linux – High Availability & Load Balancing for Network Interfaces

### 📘 **What is NIC Bonding?**

NIC bonding (also called NIC teaming or link aggregation) allows combining multiple NICs into a single logical interface for:

* Redundancy (failover)
    
* Increased bandwidth (load balancing)
    

### 📌 **Why NIC Bonding?**

* In critical environments (datacenters, HA clusters), you can’t afford a NIC failure.
    
* Bonding ensures availability even if one NIC or cable fails.
    

---

### 🏁 RHEL 6 and Earlier – Legacy NIC Bonding

#### 🔹 Configuration Files:

Bonding was traditionally done via:

* `/etc/modprobe.conf`
    
* `/etc/sysconfig/network-scripts/ifcfg-bond*`
    
* `/etc/sysconfig/network-scripts/ifcfg-eth*` (slave interfaces)
    

#### 📘 Example:

**/etc/sysconfig/network-scripts/ifcfg-bond0**

```bash
DEVICE=bond0
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
ONBOOT=yes
BOOTPROTO=none
BONDING_OPTS="mode=1 miimon=100"
```

**/etc/sysconfig/network-scripts/ifcfg-eth0**

```bash
DEVICE=eth0
MASTER=bond0
SLAVE=yes
ONBOOT=yes
```

#### 🔍 Modes:

| Mode | Description |
| --- | --- |
| 0 | Round-robin (load balancing) |
| 1 | Active-backup (failover) |
| 2 | XOR (based on MAC/IP hash) |
| 4 | 802.3ad (LACP - requires switch config) |
| 5 | Adaptive transmit load balancing |
| 6 | Adaptive load balancing |

#### 🧰 Real Use Case (RHEL 6):

On a critical production DB server with dual NICs, you configure bonding in mode 1. If one NIC or cable fails, traffic continues on the other NIC seamlessly — no downtime.

---

## 🆕 RHEL 7/8/9 – Network Teaming (Successor of Bonding)

Bonding is still supported, but **Network Teaming** was introduced as a more flexible and modern replacement. However, **bonding remains popular** due to compatibility.

#### 🔧 Create Bond Using `nmcli` (RHEL 7+)

```bash
nmcli connection add type bond ifname bond0 mode active-backup
nmcli connection add type ethernet ifname enp0s3 master bond0
nmcli connection add type ethernet ifname enp0s8 master bond0
nmcli connection modify bond0 ipv4.addresses 192.168.0.50/24 ipv4.method manual
nmcli connection up bond0
```

> ✅ Pro Tip: You can also configure using `nmtui` (Text UI) or `nm-connection-editor` (GUI).

#### 🔍 Check Status:

```bash
cat /proc/net/bonding/bond0
nmcli device status
```

---

### 🤔 Bonding vs Teaming

| Feature | Bonding | Teaming |
| --- | --- | --- |
| Kernel support | Kernel module | User-space daemon |
| Flexibility | Less | More control via `teamd` |
| Config Complexity | Simple | Slightly complex |
| Performance | Good | Better in multi-core systems |

> 💬 **Note**: Teaming is more flexible, but bonding is still widely used and supported across all enterprise Linux distros.

---

### 📌 Real-Time Use Case

**Use Case 1**: On a banking application server that needs 24/7 uptime, you use **active-backup** bonding mode. If one network interface or switch port fails, the second interface seamlessly takes over.

**Use Case 2**: In a high-performance computing (HPC) cluster, you configure bonding in **802.3ad (LACP)** mode, enabling higher throughput by aggregating NIC bandwidth with switch support.

---

## 🔧 **Network Utilities in Linux – Configure Network the Smart Way**

In RHEL 7, 8, and 9, **NetworkManager** is the default tool for managing network connections. It provides three major ways to configure the network:

* `nmtui` – A Text User Interface (TUI)
    
* `nmcli` – A Command Line Interface
    
* `nm-connection-editor` – A GUI tool (for desktop environments)
    

Let’s explore each one with explanation, examples, and real-world use cases.

---

### 🖥️ 1. `nmtui` – Text User Interface (Beginner Friendly)

#### 🔍 **What is it?**

`nmtui` provides a simple, ncurses-based interface to configure network settings from the terminal.

#### ✅ **When to Use:**

* In headless servers where GUI is not available
    
* When you want a guided interface without remembering command syntax
    

#### 📌 **How to Launch:**

```bash
nmtui
```

#### 🧭 **What You Can Do:**

* Edit a connection
    
* Activate a connection
    
* Set hostnames
    

#### 🛠️ **Example Use Case:**

You’re on a fresh RHEL VM without a GUI and want to set a static IP:

1. Run `nmtui`
    
2. Choose “Edit a connection”
    
3. Select your interface (e.g., `ens33`)
    
4. Set static IP, gateway, DNS
    
5. Save and activate
    

🚀 This is especially useful when teaching networking to beginners or doing quick changes in testing environments.

---

### 💻 2. `nmcli` – Command Line Interface (Power Users & Scripting)

#### 🔍 **What is it?**

`nmcli` is a powerful command-line tool to **create, modify, display, and delete** network connections.

#### ✅ **When to Use:**

* Automating network configuration with scripts
    
* Remote management via SSH
    
* Managing multiple interfaces or bonds/bridges
    

---

#### 🧰 **Basic Commands:**

Check device status:

```bash
nmcli device status
```

View all connections:

```bash
nmcli connection show
```

Assign static IP:

```bash
nmcli con mod ens33 ipv4.addresses 192.168.0.100/24
nmcli con mod ens33 ipv4.gateway 192.168.0.1
nmcli con mod ens33 ipv4.dns "8.8.8.8 8.8.4.4"
nmcli con mod ens33 ipv4.method manual
nmcli con up ens33
```

Enable DHCP:

```bash
nmcli con mod ens33 ipv4.method auto
nmcli con up ens33
```

---

#### 🧪 **Real Use Cases:**

* Cloud environments (AWS, GCP, Azure) where you manage servers via SSH
    
* Configuring bonding, bridging, VLANs during automation setup
    
* Troubleshooting via remote terminal when GUI is not installed
    

---

### 🖱️ 3. `nm-connection-editor` – GUI Tool for Desktop

#### 🔍 **What is it?**

This is a graphical interface to configure your network. It's often launched by network icon in GNOME or directly via:

```bash
nm-connection-editor
```

#### ✅ **When to Use:**

* Desktop environments like RHEL Workstation, Fedora, Ubuntu
    
* For quick configuration without typing commands
    

#### 🎯 **Features:**

* Create or edit wired and wireless connections
    
* Configure proxy, VPN, DNS, IPv6
    
* View device details and security settings
    

---

### 💼 Real World Examples:

| Tool | Environment | Use Case |
| --- | --- | --- |
| `nmtui` | CLI-only server | Quick IP setup without needing full syntax |
| `nmcli` | Automation scripts | Mass server configuration using Ansible |
| `nm-connection-editor` | Desktop | GUI-friendly network setup and troubleshooting |

---

## ✅ Wrap-up for Network Utilities

NetworkManager tools simplify how you manage network interfaces, both in headless environments and desktop setups. Understanding all three utilities (`nmtui`, `nmcli`, `nm-connection-editor`) allows you to confidently handle any scenario — manual setups, automation, or user-friendly GUI needs.