---
title: "Chapter 20 : Automate Everything: Podman, Kickstart and DHCP for Modern Linux Admins"
seoTitle: "Automate with Podman, Kickstart & DHCP"
seoDescription: "Automate deployments using Podman, Kickstart, and DHCP to enhance RHEL security and streamline provisioning"
datePublished: Tue Apr 22 2025 22:43:49 GMT+0000 (Coordinated Universal Time)
cuid: cm9t3dadt000009jv0jmy5f8c
slug: automate-everything-podman-kickstart-and-dhcp-for-modern-linux-admins
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745361776316/b1d10089-888c-4948-8e35-6b8895a929d8.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1745361809601/b5d05c3e-f4dd-4d71-bce6-df8cf03178cd.png

---

## 🐳 **Containers with Podman – Run, Build, and Manage Containers in RHEL**

### 🧠 **Why use Containers (and Podman)?**

In traditional application deployment, developers write code on one system (say their laptop), and sysadmins deploy it on a completely different environment (say a staging or production server). This leads to the classic problem:

> “It worked on my machine!”

Containers solve this by packaging applications with **everything they need to run**—code, runtime, libraries, environment variables—into one unit. Think of it like a shipping container: no matter where it's sent, it's compatible with the system that handles it.

Now, why **Podman**?

* Docker was once the go-to container tool, but **Docker is not supported in RHEL 8+**.
    
* Red Hat introduced **Podman**, a daemon-less, rootless, secure, and Docker-compatible container engine.
    
* Podman doesn't require a background service and uses standard Linux tools.
    
* It works well in **enterprise** environments where **security and compliance** are top concerns.
    

### 🔍 **What is a Container?**

A container is an isolated, lightweight unit that runs a process with its own filesystem, networking, and resources. It's not a full-blown VM but shares the host kernel, making it efficient and fast.

Podman allows you to:

* **Run** containers from images
    
* **Build** container images using Buildah
    
* **Manage** container lifecycle and logs
    
* **Create Pods** (like Kubernetes pods)
    
* **Auto-start containers with systemd**
    

Key terms:

* **Image** – a blueprint to run containers.
    
* **Container** – a running instance of an image.
    
* **Pod** – a group of containers sharing network and storage (K8s compatible).
    
* **Rootless** – runs containers as a non-root user, enhancing security.
    

### 🛠️ **How to Use Podman: Step-by-Step**

---

#### 🧱 1. **Installation**

```bash
dnf install podman -y         # RHEL 8/9
podman --version              # Check version
```

If you're used to Docker:

```bash
alias docker=podman           # Treat Podman as Docker
```

---

#### 🔍 2. **Search & Pull Images**

```bash
podman search httpd
podman pull docker.io/library/httpd
podman images                 # View downloaded images
```

---

#### 🚀 3. **Run Containers**

```bash
# Run Apache in a container on port 8080
podman run -dt -p 8080:80 docker.io/library/httpd

# Run more containers on different ports
podman run -dt -p 8081:80 docker.io/library/httpd
podman run -dt -p 8082:80 docker.io/library/httpd
```

Check running containers:

```bash
podman ps
```

Stop/start containers:

```bash
podman stop <container-id>
podman start <container-id>
```

---

#### 🔍 4. **Container Logs and Info**

```bash
podman logs -l
podman info                  # Environment, storage, registry info
```

---

#### 📦 5. **Create Named Containers**

```bash
podman create --name web1 docker.io/library/httpd
podman start web1
```

---

#### 🛠️ 6. **Systemd Integration – Auto-start on boot**

```bash
podman generate systemd --new --files --name web1
cp container-web1.service /etc/systemd/system/
systemctl enable container-web1.service
systemctl start container-web1.service
```

---

### 🔍 **Real-World Use Case: Dev to Prod Workflow**

Let’s say your developer builds a Python app with Flask and dependencies. Instead of manually installing Python and packages on every machine:

1. Developer builds a container image.
    
2. Uploads it to a registry.
    
3. Sysadmin pulls and runs it with Podman.
    
4. App works identically across dev, test, and prod!
    

No more "dependency hell" or manual configs!

---

### ⚖️ **Comparison: Podman vs Docker**

| Feature | Docker | Podman |
| --- | --- | --- |
| Daemon-based | Yes | No |
| Rootless support | Partial | Full |
| Supported in RHEL | No | Yes |
| Kubernetes support | Via Compose | Via Pods |
| CLI Compatibility | Native | Docker alias |

---

## ⚙️ **Kickstart Installation in RHEL – Automate OS Deployments**

### 🧠 **Why Use Kickstart?**

Imagine deploying **hundreds of Linux servers** manually. Each one requires partitioning, timezone setting, package selection, network setup, user creation, and more.

Doing this **by hand every time is inefficient, error-prone, and time-consuming.**

This is where **Kickstart** becomes a life-saver.

> Kickstart lets you automate RHEL/CentOS installations with **zero manual input**, using a pre-written config file.

Use cases:

* Data centers spinning up VM fleets.
    
* Cloud image customization.
    
* DevOps pipelines requiring consistent OS provisioning.
    
* Creating templates for **automated installs via PXE boot or ISO**.
    

---

### 🔍 **What is Kickstart?**

Kickstart is:

* A **text file** (`ks.cfg`) containing answers to all installation prompts.
    
* Read by Anaconda (RHEL installer) during boot.
    
* Can be stored locally (on ISO), via HTTP, FTP, or NFS.
    

It defines:

* Installation type (graphical/text/automated)
    
* Language, timezone, keyboard layout
    
* Partitions and disk setup
    
* User accounts and root password
    
* Packages to install
    
* Post-installation scripts (like updates or software installs)
    

---

### 🛠️ **How to Use Kickstart – Step-by-Step**

---

#### 1️⃣ **Create a Kickstart File**

You can:

* Use an existing config from a completed install (`/root/anaconda-ks.cfg`)
    
* Or generate one using GUI:
    

```bash
dnf install system-config-kickstart   # Optional tool for GUI creation
```

**Sample Kickstart file (**`ks.cfg`):

```bash
# Use text-based install
text

# Install from DVD or HTTP repo
cdrom
# or
#url --url=http://mirror.centos.org/centos/9-stream/BaseOS/x86_64/os/

# Language and timezone
lang en_US.UTF-8
keyboard us
timezone Asia/Kolkata --isUtc

# Root password (encrypted)
rootpw --iscrypted $6$somehashedpasswordhere

# Auto partition and wipe disk
clearpart --all --initlabel
autopart

# Bootloader location
bootloader --location=mbr

# No user interaction
skipx
reboot

# Network settings
network --bootproto=dhcp --device=eth0 --onboot=on

# Packages
%packages
@core
wget
net-tools
%end

# Post-installation script
%post
echo "Installation completed at $(date)" >> /var/log/kickstart-post.log
%end
```

---

#### 2️⃣ **Test the Kickstart File (Optional)**

```bash
ksvalidator ks.cfg     # Validate syntax (install pykickstart)
```

---

#### 3️⃣ **Use the Kickstart File in Installation**

You can install RHEL using a Kickstart file from:

* **Local ISO/USB**:
    
    1. Place `ks.cfg` in root of USB or ISO.
        
    2. Boot installer, hit **Tab** on boot menu.
        
    3. Enter:
        
        ```bash
        linux inst.ks=hd:LABEL=MYUSB:/ks.cfg
        ```
        
* **HTTP/NFS/FTP Server**:
    
    1. Place `ks.cfg` in web root (e.g. `/var/www/html/ks.cfg`)
        
    2. Boot target machine via PXE or ISO.
        
    3. Enter:
        
        ```bash
        linux inst.ks=http://192.168.1.100/ks.cfg
        ```
        
* **PXE Boot with Kickstart (Advanced Setup)**: Combine Kickstart with TFTP and DHCP for full network-based deployment. Add this in your `pxelinux.cfg/default`:
    
    ```bash
    append initrd=initrd.img inst.ks=http://192.168.1.100/ks.cfg
    ```
    

---

#### 💡 **Real-World Use Case: Clone 50 RHEL Servers**

Let’s say you need to install 50 identical VMs:

* Create one master VM.
    
* Export its `anaconda-ks.cfg`.
    
* Adjust disk and network settings if needed.
    
* Use that Kickstart file for PXE boot or ISO customization.
    
* Done! 50 servers in minutes, all uniform.
    

---

#### 🔁 **Make a Custom ISO with Kickstart**

For offline installs or cloud images:

1. Mount ISO and copy contents:
    

```bash
mkdir /mnt/iso /tmp/customiso
mount -o loop RHEL9.iso /mnt/iso
cp -r /mnt/iso/* /tmp/customiso/
```

2. Add `ks.cfg` and edit bootloader:
    

```bash
cp ks.cfg /tmp/customiso/
vi /tmp/customiso/isolinux/isolinux.cfg
# Append inst.ks=cdrom:/ks.cfg to boot command line
```

3. Rebuild ISO:
    

```bash
mkisofs -o RHEL9-KS.iso -b isolinux/isolinux.bin -c isolinux/boot.cat \
  -no-emul-boot -boot-load-size 4 -boot-info-table -R -J -v -T /tmp/customiso/
```

---

### 📌 Tips

* Use `%pre` and `%post` for advanced logic (like BIOS vs UEFI).
    
* Always test `ks.cfg` with `ksvalidator`.
    
* For RHCSA/RHCE exams, Kickstart is often tested—know its layout well.
    

---

## 📡 **DHCP Server Configuration on RHEL – Simplify IP Management**

### 🧠 **Why Use a DHCP Server?**

In any **dynamic network environment**, manually assigning IP addresses to every device is inefficient and error-prone.

A **DHCP (Dynamic Host Configuration Protocol)** server:

* **Automatically assigns IPs** and related settings (subnet mask, gateway, DNS)
    
* Prevents conflicts and eases network administration
    
* Essential in offices, labs, data centers, PXE boot environments
    

> Think of DHCP as a digital receptionist handing out room keys (IP addresses) to devices when they join your network.

---

### 🔍 **What is a DHCP Server?**

A **DHCP server** assigns:

* IP addresses (from a defined pool)
    
* Subnet mask
    
* Default gateway
    
* DNS servers
    
* Lease duration (how long a device can keep the IP)
    

RHEL-based systems use the `dhcp-server` package. Internally, it runs `dhcpd` and uses the main config file `/etc/dhcp/dhcpd.conf`.

---

### 🛠️ **How to Configure DHCP Server – Step-by-Step**

---

#### 1️⃣ **Install the DHCP Package**

```bash
sudo dnf install dhcp-server -y
```

---

#### 2️⃣ **Edit the DHCP Configuration File**

```bash
sudo vi /etc/dhcp/dhcpd.conf
```

Here’s a sample config:

```bash
# Global settings
default-lease-time 600;
max-lease-time 7200;
authoritative;

# Define subnet and IP range
subnet 192.168.10.0 netmask 255.255.255.0 {
  range 192.168.10.100 192.168.10.200;
  option routers 192.168.10.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 8.8.4.4;
}
```

To assign a **static IP** based on MAC address:

```bash
host printer1 {
  hardware ethernet 00:11:22:33:44:55;
  fixed-address 192.168.10.250;
}
```

---

#### 3️⃣ **Set the Interface for DHCP**

Edit `/etc/sysconfig/dhcpd` to specify the NIC:

```bash
DHCPDARGS=ens33   # Replace with your actual interface
```

---

#### 4️⃣ **Start and Enable the DHCP Service**

```bash
sudo systemctl enable --now dhcpd
sudo systemctl status dhcpd
```

---

#### 5️⃣ **Configure Firewall Rules**

```bash
sudo firewall-cmd --add-service=dhcp --permanent
sudo firewall-cmd --reload
```

---

#### 6️⃣ **Verify DHCP Lease Allocation**

On a DHCP client (or on the server, if using dnsmasq/dhcp relay):

```bash
journalctl -u dhcpd
cat /var/lib/dhcpd/dhcpd.leases
```

---

### 💻 **Real-World Example: PXE Boot with DHCP**

In a PXE environment:

* DHCP assigns IP + TFTP server info
    
* Add this to `/etc/dhcp/dhcpd.conf`:
    

```bash
subnet 192.168.1.0 netmask 255.255.255.0 {
  range 192.168.1.50 192.168.1.200;
  option routers 192.168.1.1;
  filename "pxelinux.0";
  next-server 192.168.1.10;  # IP of TFTP server
}
```

---

### 🧪 **Test DHCP Server (Client Side)**

1. On a client machine:
    
    ```bash
    sudo dhclient -v eth0
    ```
    
2. Confirm:
    
    ```bash
    ip a
    cat /var/lib/NetworkManager/dhclient-*.lease
    ```
    

---

### 🛠️ Troubleshooting Tips

* Make sure the interface is correct (`DHCPDARGS`).
    
* Check SELinux:
    
    ```bash
    sudo setsebool -P dhcpd_use_dns 1
    ```
    
* DHCP port: UDP 67 (server), UDP 68 (client)
    

---

## ✅ **Final Conclusion**

For Linux administrators aiming to scale, automate, and modernize their infrastructure, mastering these three core topics — **Podman**, **Kickstart**, and **DHCP** — is a game changer.

* **Podman** brings containerization into the RHEL ecosystem with rootless security and Docker-compatibility, perfect for managing isolated environments without the daemon overhead.
    
* **Kickstart** offers a seamless way to automate RHEL installations across large-scale environments, saving time and ensuring consistency.
    
* **DHCP**, the backbone of network automation, dynamically assigns IPs, reducing conflicts and manual errors in both lab and production setups.
    

Together, these tools equip sysadmins with the power to automate deployments, streamline server provisioning, and embrace container-native infrastructure — the foundation of modern Linux operations.