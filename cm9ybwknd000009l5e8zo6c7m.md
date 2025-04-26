---
title: "Chapter 23 : Advanced Storage Techniques in Linux: xfs_info, Stratis, and RAID"
seoTitle: "Linux Storage: xfs_info, Stratis, RAID Techniques"
seoDescription: "Explore Linux storage with xfs_info, Stratis, and RAID to improve performance, scalability, and redundancy efficiently"
datePublished: Sat Apr 26 2025 14:41:36 GMT+0000 (Coordinated Universal Time)
cuid: cm9ybwknd000009l5e8zo6c7m
slug: advanced-storage-techniques-in-linux-xfsinfo-stratis-and-raid
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745678442588/dff8390b-a103-484d-9438-11c52976fce7.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1745678475344/b68618a6-1211-4d85-ace7-b059e1d4bff3.png

---

In the previous blog, we explored **LVM and advanced storage management**, covering logical volume creation, extension, swap management, and snapshots.

In today’s blog, we’re taking it a step further.

We’ll explore tools and technologies that help Linux system administrators inspect, manage, and scale filesystems and storage architectures with confidence.

This blog will cover:

* 📊 Using `xfs_info` to understand XFS filesystem structure
    
* 🧠 Managing pools, snapshots, and thin provisioning with **Stratis**
    
* 💽 Understanding and implementing **RAID** levels in Linux (for redundancy & performance)
    

Whether you're a beginner trying to learn modern Linux storage or a seasoned admin planning scalable disk layouts — this chapter is for you.

Let’s begin with `xfs_info` and build up from there.

## 🔍 xfs\_info — Peek Inside Your XFS Filesystem

### 🧠 **Why?**

When you're managing a Linux system that uses the XFS filesystem (default in RHEL/CentOS 7+), it's crucial to understand the filesystem layout and metadata — especially when planning disk extensions, checking block sizes, or troubleshooting.  
`xfs_info` gives you all that detail without unmounting the filesystem — safely and instantly.

### 📘 **What is xfs\_info?**

`xfs_info` is a command-line tool that provides detailed information about an XFS-formatted filesystem. It displays layout information such as block size, inode size, log format, AG (allocation group) size, and more.

* It’s part of the `xfsprogs` package.
    
* Useful for filesystem planning, performance optimization, and diagnostics.
    

### ⚙️ **How to Use xfs\_info**

#### ✅ Basic Syntax:

```bash
xfs_info /mount/point
```

#### 📌 Example 1: View details of `/home`

```bash
xfs_info /home
```

#### 🔍 Sample Output:

```bash
meta-data=/dev/sda3              isize=512    agcount=4, agsize=1310720 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
data     =                       bsize=4096   blocks=5242880, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal               bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

This tells you:

* `block size` is 4096 bytes
    
* `log` is internal (for journaling)
    
* `agcount=4`: it uses 4 allocation groups for better parallel I/O
    

#### 📌 Example 2: View root filesystem info

```bash
xfs_info /
```

> 💡 Pro tip: You can combine this with `df -Th` to first confirm the filesystem type.

```bash
df -Th
```

---

### 💼 **Real-World Use Cases**

🔸 **Case 1: Planning for Extension** When extending a logical volume, `xfs_info` helps verify the block size and inode percentage to determine safe grow limits.

🔸 **Case 2: Performance Tuning** System admins might analyze AG count and layout for heavy read/write workloads like database servers.

🔸 **Case 3: Support/Debug** Troubleshooting filesystem corruption or logging into a remote support case? `xfs_info` provides key metadata used in root cause analysis.

---

## 🧠 Stratis – Simplifying Modern Linux Storage Management

### 🔍 Why Stratis?

Storage management in Linux has traditionally required administrators to juggle multiple layers — LVM, filesystems, encryption, RAID, etc. This complexity can lead to misconfigurations and makes storage harder to scale and automate.

**Stratis** was designed to simplify all of this. It provides:

* 🧩 **Abstraction** over LVM, XFS, and device-mapper
    
* 📦 **Integrated snapshots, thin provisioning**, and future pool management
    
* 🔒 **Built-in encryption support**
    
* 🎯 **Dynamic scaling** without worrying about low-level commands
    

Think of Stratis as the ZFS or Btrfs-like experience for Linux admins who want *ease of use without sacrificing power*.

---

### 📘 What is Stratis?

**Stratis** is a **volume-managing filesystem** for Linux built on top of existing, mature technologies like:

* **LVM** (Logical Volume Manager)
    
* **XFS** (as the underlying filesystem)
    
* **device-mapper** for encryption and snapshot support
    

It is managed via a **daemon** called `stratisd` and a CLI tool named `stratis`.

It allows admins to create *storage pools*, add/remove devices on the fly, and manage *filesystems* dynamically with user-friendly commands.

---

### 🛠️ How to Use Stratis (Step-by-Step with Example)

> 📌 **Use Case**: You want to manage `/data` storage with snapshots and dynamic scaling across multiple disks.

#### Step 1: Install Stratis packages

```bash
dnf install stratisd stratis-cli -y
systemctl enable --now stratisd
```

---

#### Step 2: Prepare block devices (e.g., `/dev/sdb` and `/dev/sdc`)

> These will be used in your storage pool.

```bash
lsblk
```

---

#### Step 3: Create a Stratis pool

```bash
stratis pool create mypool /dev/sdb /dev/sdc
```

🧠 *This creates a new storage pool named* `mypool` from those two block devices.

---

#### Step 4: Create a filesystem inside that pool

```bash
stratis filesystem create mypool myfs
```

---

#### Step 5: Mount the filesystem

```bash
mkdir /data
mount /stratis/mypool/myfs /data
```

---

#### Step 6: Make it persistent across reboots

Edit `/etc/fstab`:

```bash
/stratis/mypool/myfs /data xfs defaults,x-systemd.requires=stratisd.service 0 0
```

---

#### Bonus: Take a snapshot!

```bash
stratis filesystem snapshot mypool myfs snap1
```

🧪 This allows you to **rollback** or recover from accidental changes without LVM complexity.

---

### 📌 Real-World Benefits of Stratis

* 💡 Ideal for VM storage pools in virtualization platforms (like KVM/QEMU)
    
* 🛡️ Combine encryption + snapshots for secure storage on laptops
    
* 🚀 Easily expand developer machines with external disks dynamically
    
* 🔄 Great for container-host environments needing flexible storage pools
    

---

## 🛡️ RAID (Redundant Array of Independent Disks)

---

## 🧠 Why RAID? (Why It’s Important)

* In critical server environments, **disk failures** are inevitable.
    
* RAID ensures **data availability**, **improves performance**, and **provides redundancy** even if a disk fails.
    
* Some RAID types prioritize **speed**, some prioritize **fault tolerance**, and some balance **both**.
    

**Real-World Example:**  
Imagine you run a production database server. If the disk storing your customer data fails, you could lose everything.  
But with RAID, even if a disk fails, the server **keeps running** without data loss!

---

## 📚 What is RAID? (What Exactly It Is)

* RAID stands for **Redundant Array of Independent Disks**.
    
* It combines multiple physical disks into **one logical unit** to improve **performance**, **redundancy**, or **both**.
    
* RAID can be implemented in two ways:
    
    * **Software RAID** (managed by the OS like Linux mdadm)
        
    * **Hardware RAID** (using a RAID controller card)
        

**Popular RAID Levels:**

| RAID Level | Description | Use Case |
| --- | --- | --- |
| RAID 0 | Striping, no redundancy, high speed | Temporary data, cache servers |
| RAID 1 | Mirroring, redundancy | Critical data like databases |
| RAID 5 | Striping with parity, balanced | Web servers, file servers |
| RAID 6 | Double parity | High-availability systems |
| RAID 10 | Striping + Mirroring | Enterprise-grade applications needing high speed and redundancy |

---

## 🛠️ How to Implement RAID (Hands-On Steps)

**Software RAID with** `mdadm` in Linux

We'll create a simple RAID 1 (mirroring) setup.

---

### Step 1: Install `mdadm`

```bash
sudo yum install mdadm   # RHEL/CentOS
sudo apt install mdadm   # Ubuntu/Debian
```

---

### Step 2: Add and Identify Disks

* Attach 2 new disks (for example `/dev/sdb` and `/dev/sdc`).
    
* Verify them:
    

```bash
lsblk
fdisk -l
```

---

### Step 3: Create RAID Device

```bash
sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdb /dev/sdc
```

* `/dev/md0` is the RAID device.
    
* `--level=1` is RAID 1 (Mirroring).
    
* `--raid-devices=2` specifies 2 disks.
    

---

### Step 4: Monitor RAID Creation

```bash
cat /proc/mdstat
```

It will show the RAID syncing progress.

---

### Step 5: Create Filesystem on RAID

```bash
mkfs.ext4 /dev/md0
```

---

### Step 6: Mount and Use

```bash
mkdir /mnt/raid1
mount /dev/md0 /mnt/raid1
```

* Make it permanent by editing `/etc/fstab`.
    

---

### Step 7: Save RAID Configuration

```bash
sudo mdadm --detail --scan >> /etc/mdadm.conf
```

This ensures RAID is assembled on reboot.

---

## ⚡ Bonus Tip: Check RAID Details

```bash
mdadm --detail /dev/md0
```

Shows detailed info like state, devices, RAID level, and health.

---

# 🎯 Real-World Scenario for RAID

* Database servers use **RAID 1** or **RAID 10** for safety and performance.
    
* Backup servers use **RAID 5** or **RAID 6** to balance redundancy and storage.
    
* Critical apps like email servers or web servers **must** have RAID to ensure uptime.
    

---

## 🏁 Conclusion: Building Smarter, Resilient Storage in Linux

In today's Linux environments, **storage management** isn't just about adding disks — it's about **building resilient, scalable, and high-performing systems**.

* With `xfs_info`, you learned how to **analyze and monitor your XFS filesystems** like a pro, ensuring efficient usage of disk space and filesystem structures.
    
* Stratis brought the **next-generation storage management** experience — combining LVM + filesystem handling into a **simplified, flexible** workflow with **automatic filesystem extension**.
    
* RAID introduced you to **fault-tolerant disk setups** — ensuring your systems **stay online and protected** even if hardware fails, whether using software RAID with `mdadm` or hardware RAID solutions.
    

💡 **Mastering these technologies** not only boosts your technical skills but also **builds your confidence** as a Linux administrator — ready to handle real-world production challenges.

🚀 **Next Steps:**

* Practice setting up Stratis pools, RAID arrays, and filesystem expansion on test VMs.
    
* Monitor performance and fault scenarios.
    
* Design storage layouts based on the needs of your Linux servers.
    

The future of storage is not just about size — it’s about **strategy and smart management**.  
Keep learning, keep building!