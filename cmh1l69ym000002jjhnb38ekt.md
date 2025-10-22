---
title: "🧩 Linux Topics Flow"
datePublished: Wed Oct 22 2025 06:02:20 GMT+0000 (Coordinated Universal Time)
cuid: cmh1l69ym000002jjhnb38ekt
slug: linux-topics-flow

---

* **System Information & Monitoring Commands** (`uname`, `uptime`, `top`, `free`, `vmstat`, `iostat`, `sar`)
    
* **Process Management** (`ps`, `top`, `kill`, `nice`, `renice`, `systemctl`)
    
* **Disk & Filesystem Management** (`df`, `du`, `lsblk`, `mount`, `fstab`, `xfs_info`)
    
* **Networking** (`ip`, `ss`, `ping`, `traceroute`, `dig`, `netstat`, `nmcli`)
    
* **User & Permission Management** (`useradd`, `usermod`, `chmod`, `sudo`, `pam`, `sudoers`)
    
* **System Logs & Rsyslog/journalctl**
    
* **Package & Service Management** (`yum`, `dnf`, `rpm`, `systemctl`)
    
* **SELinux & Firewall Basics** (`sestatus`, `setenforce`, `firewalld`)
    
* **Crontab & Scheduled Jobs**
    
* **Boot Process & Runlevels (targets)**
    

## 🧩 **Topic 1: System Information & Monitoring Commands**

### 🔹 Why This Topic Matters

As a Production Systems Engineer or Linux Admin, you’ll constantly need to **check system health**, **analyze performance**, and **gather environment details** — especially during incidents (like CPU spikes, slow apps, or server load issues).

Knowing these commands **saves time** and **prevents escalation** in live environments.

---

## 🔸 1. System Information Commands

| Command | Purpose | Example | Real-World Use Case |
| --- | --- | --- | --- |
| `uname -a` | Shows all system information | `Linux srv01 5.14.0-384.el9.x86_64 #1 SMP ...` | Check kernel, OS, and architecture when troubleshooting compatibility or security patches. |
| `hostnamectl` | Displays or sets hostname | `hostnamectl set-hostname` [`web01.prod`](http://web01.prod)`.local` | Used during provisioning or fixing hostname mismatches in monitoring tools. |
| `cat /etc/os-release` | Shows OS version info | `NAME="Red Hat Enterprise Linux" VERSION="9.2"` | Verifying OS version before package installation or kernel update. |
| `uptime` | Shows how long the system is running and load average | `10:24:03 up 12 days, 3:02, 2 users, load average: 0.45, 0.22, 0.12` | Used to check server stability and CPU load. |
| `who` or `w` | Shows logged-in users | `revanth pts/0 2025-10-22 09:01 (:0)` | Audit who’s logged in during maintenance or deployment windows. |
| `last` | Shows login history | \`last | head\` |

---

## 🔸 2. Performance & Monitoring Commands

### 🔹 `top`

Shows live CPU, memory, process info.

**Example:**

```bash
top
```

**Key Metrics:**

* `%Cpu(s):` — CPU usage (user/system/idle)
    
* `KiB Mem` — RAM usage
    
* `PID`, `USER`, `%CPU`, `%MEM`, `COMMAND`
    

**Real-time Use Case:**  
During an incident:

```bash
top -p $(pidof httpd)
```

To monitor only Apache processes.

**Interview Tip:**

> How do you find which process is consuming the most CPU?  
> Answer: `top` or `ps -eo pid,comm,%cpu,%mem --sort=-%cpu | head`

---

### 🔹 `vmstat`

Displays system performance statistics (CPU, memory, I/O).

**Example:**

```bash
vmstat 2 5
```

→ updates every 2 sec, 5 times.

**Fields:**

* `r` – running processes
    
* `b` – blocked processes
    
* `si/so` – swap in/out
    
* `us/sy/id` – user/system/idle CPU usage
    

**Real Case:**  
You suspect CPU saturation — run `vmstat` to see if processes are blocked or waiting for I/O.

---

### 🔹 `iostat`

Monitors disk I/O usage.

**Example:**

```bash
iostat -x 2 3
```

**Output:**

* `%util` shows disk utilization (close to 100% = I/O bottleneck)
    
* `await` shows average wait time
    

**Real Case:**  
App team says “system slow” — check if `/dev/sda` is 99% utilized → possible disk bottleneck.

---

### 🔹 `free -m`

Displays memory usage in MB.

**Example:**

```bash
free -m
```

**Output:**

```bash
              total        used        free      shared  buff/cache   available
Mem:           7854        1723        1021          12        5109        5847
```

**Real Case:**  
To verify if memory is sufficient before deploying a container or heavy process.

---

### 🔹 `sar`

Historical performance data (CPU, memory, I/O, network).

**Example:**

```bash
sar -u 1 3        # CPU usage
sar -r 1 3        # Memory usage
sar -n DEV 1 3    # Network stats
```

**Real Case:**  
You get a performance complaint at 10 AM — but the issue happened overnight.  
You can check:

```bash
sar -u -f /var/log/sa/sa21
```

to view CPU history for that day.

---

## 🧰 Interview-Focused Questions

| Question | Sample Answer |
| --- | --- |
| How do you check CPU utilization in Linux? | `top`, `uptime`, `mpstat`, or `sar -u` |
| What’s the difference between load average and CPU utilization? | Load average = processes waiting for CPU or I/O. CPU utilization = percentage of CPU busy time. |
| How do you find memory usage per process? | \`ps aux --sort=-%mem |
| How do you monitor disk performance? | `iostat -x`, `sar -d`, or `iotop` |
| If a system is slow, what’s your first approach? | Check load average (`uptime`), CPU usage (`top`), memory (`free -m`), disk I/O (`iostat`), and logs (`/var/log/messages`). |

---

## ⚙️ Real-World Scenarios (Production Level)

### 🔸 Scenario 1:

**Problem:** Web app is slow, but CPU usage is only 5%.  
**Steps:**

1. Check disk I/O → `iostat -x`
    
2. Found `/dev/sda` at 98% util — disk bottleneck.
    
3. Action → Migrate logs to secondary disk.
    

---

### 🔸 Scenario 2:

**Problem:** System unresponsive at night.  
**Steps:**

1. Use `sar -u -f /var/log/sa/sa21` → saw 100% CPU from cron job.
    
2. Identify job via `/var/log/cron` → large backup script.
    
3. Solution → reschedule at off-peak hours.
    

---

### 🧩 Recent/Tricky Issue (2024–2025 Trend)

* **RHEL 9 / Rocky 9 systems** sometimes show delayed `top` refresh due to newer `procfs` behavior — fixed in newer kernels.
    
* **Containers** (Podman/Docker) often show different CPU/mem usage from host — always run `top` inside the container for accuracy.
    

## 🧩 Hands-On Task: System Health Snapshot

### **Goal:**

Gather a **complete snapshot of your system** — kernel, CPU, memory, disk, and load — using commands we discussed.

---

### **Step 1: Kernel & OS Info**

```bash
# Check kernel and architecture
uname -a

# Check OS version
cat /etc/os-release

# Check hostname and system info
hostnamectl
```

**Task:** Note down kernel version, OS version, and hostname.

---

### **Step 2: Uptime & Load**

```bash
# Check uptime and load average
uptime

# Check logged-in users
who
w
```

**Task:** Identify how many users are logged in and the current load average. Compare load with number of CPU cores.

---

### **Step 3: CPU Usage**

```bash
# Live CPU usage
top -n 1

# CPU stats over 5 seconds
vmstat 2 3
```

**Task:** Find the process using the most CPU and note user/system/idle percentages.

---

### **Step 4: Memory Usage**

```bash
# Memory snapshot
free -m

# Memory usage per process
ps aux --sort=-%mem | head
```

**Task:** Identify total RAM, free RAM, and top 3 memory-consuming processes.

---

### **Step 5: Disk & I/O**

```bash
# Disk usage
df -h

# Disk I/O stats
iostat -xz 2 3
```

**Task:** Identify the disk with highest utilization and its I/O wait.

---

### **Step 6: Historical Performance**

```bash
# CPU history
sar -u 1 3

# Memory history
sar -r 1 3
```

**Task:** See if the CPU or memory was unusually high during the check.

---

### ✅ Optional Challenge (Bonus Production Scenario)

* Run `top` for 1 minute while you trigger a small CPU-intensive task (`stress -c 2`) if `stress` is installed.
    
* Observe how CPU `%us` and `%sy` change.
    

---

### **Outcome**

After this task, you should be able to:

* Quickly check **system info, load, CPU, memory, disk, and users**.
    
* Analyze **bottlenecks** in a few minutes.
    
* Explain findings **in an interview scenario**.
    

---

let’s move to **Topic 2: Process Management**. This is **one of the most important Linux topics** for Production Systems Engineer roles.

We’ll cover:

* Core commands
    
* Real-world usage
    
* Interview questions
    
* Recent production issues
    

---

## 🔹 **Topic 2: Process Management**

### 🔹 Why This Topic Matters

As a Production Systems Engineer, **processes are the heart of Linux systems**.  
You need to:

* Identify CPU/memory-hogging processes
    
* Kill or restart stuck processes
    
* Adjust priorities for critical services
    
* Troubleshoot background jobs, daemons, and container processes
    

**Interview angle:** Recruiters often ask:

> “How do you find and manage processes consuming high CPU or memory?”  
> “How do you handle a zombie process?”  
> “Explain `nice` and `renice`.”

---

## 🔸 1. Listing Processes

| Command | Purpose | Example | Real-World Use Case |
| --- | --- | --- | --- |
| `ps -ef` | Show all running processes | \`ps -ef | grep httpd\` |
| `ps aux` | Shows detailed process info with %CPU and %MEM | \`ps aux --sort=-%cpu | head\` |
| `top` | Interactive, real-time view | Press `P` for CPU sort, `M` for memory | Monitor process spikes in production |
| `htop` | Enhanced interactive process viewer | Scroll, sort, kill processes easily | Visual tool for quickly finding bottlenecks |

**Interview Tip:**

> “Difference between `ps -ef` and `ps aux`?”

* `ps -ef`: BSD style, shows UID, PID, PPID, start time, command
    
* `ps aux`: BSD style with CPU/Memory columns, easier for sorting usage
    

---

## 🔸 2. Managing Processes

| Command | Purpose | Example | Real-World Use Case |
| --- | --- | --- | --- |
| `kill PID` | Send default SIGTERM to stop a process | `kill 1234` | Gracefully stop a stuck process |
| `kill -9 PID` | Force kill a process (SIGKILL) | `kill -9 1234` | Stop a zombie/hanging process immediately |
| `pkill` | Kill by process name | `pkill httpd` | Restart services in bulk without finding PID manually |
| `jobs / fg / bg` | Manage background jobs | `sleep 100 &; jobs; fg %1` | Resume or bring background scripts to foreground |
| `nohup` | Run process immune to logout | `nohup` [`myscript.sh`](http://myscript.sh) `&` | Keep long-running scripts running after SSH logout |

---

## 🔸 3. Process Priorities

* `nice` — Start a process with a priority level
    

```bash
nice -n 10 ./backup.sh
```

* `renice` — Change priority of a running process
    

```bash
renice -n -5 -p 1234
```

**Real-World Use Case:**

* During peak hours, you can **lower priority of batch jobs** and **increase priority of critical app processes**.
    

**Interview Tip:**

> What does a negative nice value mean?

* Negative = higher priority
    
* Positive = lower priority
    

---

## 🔸 4. Zombie and Orphan Processes

* **Zombie process:** Completed execution but still has PID in process table (parent hasn’t `wait()`ed).
    
* **Orphan process:** Parent dies before child — gets adopted by `init`/`systemd`.
    

**Check zombies:**

```bash
ps aux | grep Z
```

**Real Case:**

* In production, too many zombies → `/proc` table fills → system can refuse new processes.
    
* Fix: Restart parent service or investigate misbehaving scripts.
    

---

## 🔸 5. Systemd Service Management (Processes as Services)

| Command | Purpose |
| --- | --- |
| `systemctl status nginx` | Check status of a service |
| `systemctl start nginx` | Start service |
| `systemctl stop nginx` | Stop service |
| `systemctl restart nginx` | Restart service |
| `systemctl enable nginx` | Enable service at boot |
| `systemctl disable nginx` | Disable service at boot |

**Real-World Use Case:**

* Production web servers hang → `systemctl restart nginx` to restore service.
    
* Use `journalctl -u nginx -f` to check logs in real-time.
    

---

## 🔸 6. Useful Monitoring Commands

* `pidof <process>` → Get PID by process name
    
* `pgrep <pattern>` → Find PIDs matching pattern
    
* `lsof -p PID` → Open files and network sockets of a process
    
* `strace -p PID` → Trace system calls of a process for debugging
    

**Scenario:**

* A database is slow → `strace -p <PID>` → see which syscalls are taking time → maybe disk I/O is the bottleneck.
    

---

## 🔸 7. Interview Questions

| Question | Answer / Approach |
| --- | --- |
| How do you find the top 5 memory-consuming processes? | \`ps aux --sort=-%mem |
| How do you handle a stuck process that doesn’t respond to normal `kill`? | Use `kill -9 PID` after checking the process isn’t critical. |
| What’s the difference between a daemon and a normal process? | Daemon: runs in background, usually started by init/systemd, no terminal attached. |
| How do you prevent a long-running script from stopping after SSH logout? | `nohup` [`script.sh`](http://script.sh) `&` or `screen/tmux` |

---

## 🔹 Real-World Production Scenarios

### Scenario 1 — CPU Spike

* Users report slow app performance.
    
* Check top processes: `top` → find `java` process at 100% CPU.
    
* Use `renice -n 10 PID` to lower batch job priority.
    
* Investigate batch job logs for optimization.
    

### Scenario 2 — Zombie Processes

* System has hundreds of zombies: `ps aux | grep Z`
    
* Find parent: `pstree -p`
    
* Restart parent service → zombies cleared.
    

### Scenario 3 — Background Job Management

* Deploying a script on multiple servers via SSH.
    
* Run with `nohup` [`deploy.sh`](http://deploy.sh) `&`
    
* Use `jobs` and `fg` if you need to bring it back interactively.
    

---

## ✅ **Hands-On Task: Process Management**

1. List all processes and sort by CPU usage.
    

```bash
ps aux --sort=-%cpu | head
```

2. Find the PID of a background process (e.g., sleep) and stop it.
    

```bash
sleep 300 &  
ps aux | grep sleep  
kill <PID>
```

3. Start a process with lower priority and then increase priority for a critical process.
    

```bash
nice -n 10 ./backup.sh &  
renice -n -5 -p <critical_PID>
```

4. Check for zombie processes:
    

```bash
ps aux | grep Z
```

5. Restart a systemd service (e.g., nginx) and check its logs:
    

```bash
systemctl restart nginx  
journalctl -u nginx -f
```

---

Once you complete this **hands-on practice**, you’ll be able to:

* Identify and manage processes quickly.
    
* Prioritize critical workloads.
    
* Debug stuck, orphan, or zombie processes.
    
* Handle service-level processes using systemd.
    

---

let’s move to **Topic 3: Disk & Filesystem Management**. This is **critical for Production Systems Engineers**, because disk issues are a **common cause of production outages**.

We’ll cover:

* Commands
    
* Real-world scenarios
    
* Interview questions
    
* Hands-on tasks
    

---

## 🔹 **Topic 3: Disk & Filesystem Management**

### 🔹 Why This Topic Matters

Disk and filesystem management is crucial for:

* Ensuring **enough space for apps and logs**
    
* **Preventing I/O bottlenecks**
    
* Handling **mounting, resizing, and monitoring disks**
    
* Troubleshooting **storage-related slowdowns**
    

**Interview Angle:**

> “How do you check disk usage?”  
> “What’s the difference between `df` and `du`?”  
> “How do you find the top directories consuming space?”

---

## 🔸 1. Checking Disk Usage

| Command | Purpose | Example | Real-World Use Case |
| --- | --- | --- | --- |
| `df -h` | Show filesystem disk usage | `df -h` | Check available space on all mounted filesystems |
| `du -sh /var/log/*` | Show directory size | `du -sh /var/log/*` | Find which log directories consume most space |
| `lsblk` | List block devices | `lsblk` | Identify new disks or partitions |
| `blkid` | Show UUID & filesystem type | `blkid /dev/sdb1` | Needed when editing `/etc/fstab` for mounts |
| `mount` | Check mounted filesystems | \`mount | grep /mnt\` |

**Interview Tip:**

> How to find the top 5 directories consuming space?

```bash
du -ah / | sort -rh | head -5
```

---

## 🔸 2. Disk I/O Monitoring

| Command | Purpose | Example | Real-World Use Case |
| --- | --- | --- | --- |
| `iostat -xz 2 3` | Check I/O per disk | `iostat -xz` | Find disks with high utilization or wait time |
| `iotop` | Real-time disk I/O per process | `iotop -o` | Identify which process is causing I/O bottlenecks |
| `df -i` | Check inode usage | `df -i` | Prevent “No space left on device” due to inodes, not blocks |

**Scenario:**  
Disk full error, but `df -h` shows free space → check `df -i` → inodes exhausted.

---

## 🔸 3. Mounting & Unmounting Disks

```bash
# Mount a disk
mount /dev/sdb1 /mnt/data

# Check mounts
mount | grep /mnt

# Unmount safely
umount /mnt/data
```

**/etc/fstab:**

* Auto-mount disks at boot
    

```bash
UUID=<disk-uuid> /mnt/data xfs defaults 0 0
```

**Real Case:** Misconfigured fstab → system hangs at boot → fix in rescue mode.

---

## 🔸 4. Filesystem Types & Info

| Command | Purpose | Example | Use Case |
| --- | --- | --- | --- |
| `file -s /dev/sdb1` | Check filesystem type | `file -s /dev/sdb1` | Verify before formatting or mounting |
| `xfs_info /mnt/data` | XFS filesystem info | `xfs_info /mnt/data` | Check allocation, metadata, and block size |
| `tune2fs -l /dev/sda1` | EXT filesystem info | `tune2fs -l /dev/sda1` | Check filesystem parameters, last fsck, reserved blocks |

**Interview Tip:**

> Difference between ext4 and xfs?

* ext4: older, general-purpose
    
* xfs: better for large files, high-performance workloads, and production servers
    

---

## 🔸 5. Disk Partitioning & LVM Basics

* **Partitioning** (fdisk, parted)
    

```bash
fdisk -l /dev/sdb
parted /dev/sdb mkpart primary xfs 0% 100%
```

* **Logical Volume Management (LVM)**:
    

```bash
pvcreate /dev/sdb1
vgcreate vg_data /dev/sdb1
lvcreate -L 10G -n lv_data vg_data
mkfs.xfs /dev/vg_data/lv_data
mount /dev/vg_data/lv_data /mnt/data
```

**Real Case:**

* Adding storage without downtime: attach new disk → create LVM → extend existing filesystem.
    

---

## 🔸 6. Common Disk Issues in Production

| Issue | Symptoms | Commands to Diagnose |
| --- | --- | --- |
| Full disk | “No space left” errors | `df -h`, `du -sh /var/log/*` |
| High I/O | Slow apps | `iostat -xz`, `iotop` |
| Inodes exhausted | Can’t create files despite free space | `df -i` |
| Mount failure | Disk not accessible | \`dmesg |
| Filesystem corruption | Unmount fails or fsck needed | `xfs_repair`, `fsck` |

**Interview Tip:**

> How do you handle a full `/var/log` on a running server?

* Find large files (`du -sh /var/log/*`)
    
* Compress or rotate logs (`logrotate`)
    
* Clean old or unnecessary logs
    

---

## 🔹 **Hands-On Task: Disk & Filesystem**

1. Check current disk usage and filesystem types:
    

```bash
df -hT
lsblk -f
```

2. Find top 5 directories consuming space:
    

```bash
du -ah / | sort -rh | head -5
```

3. Monitor disk I/O for 10 seconds:
    

```bash
iostat -xz 2 5
```

4. Check inode usage:
    

```bash
df -i
```

5. (Optional) Add a new disk using LVM and mount it at `/mnt/data`
    

* `pvcreate`, `vgcreate`, `lvcreate`, `mkfs.xfs`, `mount`
    

---

✅ **Outcome:**  
After this topic, you will:

* Quickly identify disk and filesystem issues
    
* Monitor I/O and inodes
    
* Partition and mount new disks
    
* Handle production storage bottlenecks
    

---

## 🔹 **Interview-Focused Questions for Disk & Filesystem**

| Question | Answer / Approach |
| --- | --- |
| How do you find the largest directories on a Linux system? | \`du -ah / |
| Difference between `df` and `du`? | `df` shows **filesystem usage**, `du` shows **directory/file usage**. |
| How do you check disk space used by inodes? | `df -i` |
| How do you monitor disk I/O per process? | `iotop -o` or `pidstat -d` |
| How do you safely expand an XFS filesystem? | Use `lvextend -L +10G /dev/vg/lv` and `xfs_growfs /mountpoint` |
| Difference between EXT4 and XFS? | EXT4: traditional, general-purpose. XFS: high-performance, large files, production workloads. |
| How to fix filesystem corruption? | `fsck` for ext4, `xfs_repair` for XFS; always **unmount first**. |
| How do you detect a disk bottleneck? | `iostat -xz 2 3`, look at `%util` and `await`. |

---

## 🔹 **Real-Time Production & Recent Issues (2024–2025)**

1. **XFS Log Corruption on RHEL 9 / Rocky 9:**
    
    * Symptom: Server hangs when writing large log files.
        
    * Cause: Improper shutdown during heavy write load on XFS filesystem.
        
    * Fix: `xfs_repair` in maintenance mode; enable `nobarrier=0` if on virtual disks with proper hardware write cache.
        
2. **Inode Exhaustion on Containers:**
    
    * Symptom: `touch: cannot create file: No space left on device` even though `df -h` shows free space.
        
    * Cause: Thousands of small temp files created by apps or Docker containers.
        
    * Fix: Clean `/var/lib/docker/tmp` or `/tmp`, or increase inodes when formatting new disk (`mkfs.xfs -i size=512`).
        
3. **High I/O Wait During Backups:**
    
    * Symptom: Applications slow during nightly backups.
        
    * Diagnosis: `iostat -xz` shows `%util ~100%` on `/dev/sdb`.
        
    * Solution: Schedule backups during off-peak hours, move backup disk to separate volume, or throttle backup I/O (`ionice`).
        
4. **Mount Failure After VM Resize or Disk Replacement:**
    
    * Symptom: VM fails to boot after storage resize.
        
    * Cause: `/etc/fstab` misconfiguration with old UUID.
        
    * Fix: Boot into rescue mode → correct UUID (`blkid`) → reboot.
        
5. **Disk Full on Logs Without Notification:**
    
    * Symptom: `/var/log/messages` grows and fills root partition.
        
    * Cause: logrotate misconfiguration or disabled cron job.
        
    * Fix: Configure `logrotate`, compress old logs, monitor disk usage with automated alerts.
        

---

### ✅ **Hands-On Enhancement Tasks**

1. Check inodes usage for `/var` and `/home`:
    

```bash
df -i
```

2. Simulate a high I/O scenario using a large file copy:
    

```bash
dd if=/dev/zero of=/tmp/bigfile bs=1M count=2048
iostat -xz 2 5
```

* Observe `%util` and `await`.
    

3. Identify recently modified large files (&gt;500MB) in `/var/log`:
    

```bash
find /var/log -type f -size +500M -exec ls -lh {} \;
```

4. Verify and compare filesystem type for disks in `/mnt`:
    

```bash
lsblk -f
file -s /dev/sdb1
```

---

💡 **Summary of What You Now Know for Interviews & Production:**

* How to **identify space or inode issues** quickly.
    
* How to **diagnose I/O bottlenecks** using `iostat` and `iotop`.
    
* How to **mount, unmount, and expand filesystems** safely.
    
* Handling **recent issues on RHEL 9 / containers / XFS**.
    
* Commands to **demonstrate knowledge in interviews** (top 5 directories, disk usage, inode usage, high I/O).
    

## **Topic 3: Disk & Filesystem Management**

### 1️⃣ Commands

#### **Disk Usage**

```bash
df -h            # Check space on mounted filesystems
df -i            # Check inode usage
du -sh /var/*    # Directory-wise usage
lsblk -f         # List block devices and filesystem types
blkid            # Show UUID and filesystem type
mount            # Check mounted disks
umount /mnt/data # Unmount disk safely
```

#### **Disk I/O & Performance**

```bash
iostat -xz 2 3   # I/O stats per disk
iotop -o         # Real-time disk I/O per process
```

#### **Filesystem Info & Management**

```bash
xfs_info /mnt/data        # XFS filesystem info
tune2fs -l /dev/sda1      # EXT filesystem info
file -s /dev/sdb1         # Detect filesystem type
fdisk -l /dev/sdb          # Partition info
parted /dev/sdb mkpart ... # Partition creation
mkfs.xfs /dev/sdb1         # Format disk with XFS
mount /dev/sdb1 /mnt/data  # Mount disk
```

#### **LVM Basics**

```bash
pvcreate /dev/sdb1
vgcreate vg_data /dev/sdb1
lvcreate -L 10G -n lv_data vg_data
mkfs.xfs /dev/vg_data/lv_data
mount /dev/vg_data/lv_data /mnt/data
```

---

### 2️⃣ Real-Time Production Issues / Scenarios

1. **XFS Log Corruption (RHEL 9 / Rocky 9)**
    
    * Symptom: Server hangs when writing large logs
        
    * Cause: Improper shutdown during heavy write
        
    * Fix: `xfs_repair` in maintenance mode, ensure proper hardware write cache
        
2. **Inode Exhaustion on Containers**
    
    * Symptom: “No space left” despite free disk
        
    * Cause: Too many small files, e.g., `/tmp` or Docker temp
        
    * Fix: Clean temp files or increase inode size when formatting new disk
        
3. **High I/O Wait During Backups**
    
    * Symptom: Apps slow at night
        
    * Diagnosis: `iostat -xz` shows `%util` ~100%
        
    * Solution: Off-peak scheduling, separate disk, throttle with `ionice`
        
4. **Mount Failure After Disk Replacement or VM Resize**
    
    * Symptom: VM fails to boot
        
    * Cause: Wrong UUID in `/etc/fstab`
        
    * Fix: Rescue mode → correct UUID → reboot
        
5. **Disk Full on Logs Without Alerts**
    
    * Symptom: `/var/log/messages` fills root partition
        
    * Cause: logrotate misconfigured or cron disabled
        
    * Fix: Configure `logrotate`, compress old logs, add disk monitoring alerts
        

---

## **Topic 3: Disk & Filesystem Management**

### 3️⃣ Interview Questions

| Question | Answer / Approach |
| --- | --- |
| How do you find the largest directories on a Linux system? | \`du -ah / |
| Difference between `df` and `du`? | `df` → filesystem usage, `du` → directory/file usage |
| How do you check inode usage? | `df -i` |
| How to monitor disk I/O per process? | `iotop -o` or `pidstat -d` |
| How do you safely expand an XFS filesystem? | `lvextend -L +10G /dev/vg/lv` → `xfs_growfs /mountpoint` |
| Difference between EXT4 and XFS? | EXT4: traditional, general-purpose. XFS: high-performance, large files, production workloads |
| How to fix filesystem corruption? | `fsck` for EXT4, `xfs_repair` for XFS (unmount first) |
| How to detect a disk bottleneck? | `iostat -xz 2 3` → check `%util` and `await` |
| How to handle a full `/var/log` without downtime? | Compress old logs, clean unnecessary logs, configure `logrotate` |

---

### 4️⃣ Hands-On Tasks

1. **Check disk usage and filesystem types**
    

```bash
df -hT
lsblk -f
```

2. **Find top 5 directories consuming space**
    

```bash
du -ah / | sort -rh | head -5
```

3. **Monitor disk I/O for 10 seconds**
    

```bash
iostat -xz 2 5
```

4. **Check inode usage**
    

```bash
df -i
```

5. **Simulate high I/O scenario**
    

```bash
dd if=/dev/zero of=/tmp/bigfile bs=1M count=2048
iostat -xz 2 5
```

6. **Identify recently modified large files (&gt;500MB) in** `/var/log`
    

```bash
find /var/log -type f -size +500M -exec ls -lh {} \;
```

7. **Add a new disk using LVM (optional, for practice)**
    

```bash
pvcreate /dev/sdb1
vgcreate vg_data /dev/sdb1
lvcreate -L 10G -n lv_data vg_data
mkfs.xfs /dev/vg_data/lv_data
mount /dev/vg_data/lv_data /mnt/data
```

---

✅ **Outcome:**  
After this, you’ll be able to:

* Quickly identify disk and filesystem issues
    
* Monitor I/O and inodes
    
* Mount, unmount, and expand filesystems safely
    
* Handle production storage bottlenecks
    
* Explain your approach confidently in interviews
    

---

## **Topic 4: Networking Commands & Real-Time Production Issues**

### 1️⃣ Commands

#### **Basic Network Information**

```bash
ip a              # Show IP addresses and interfaces
ip -br addr       # Brief view of interfaces and IPs (very handy in production)
ip link show      # Show interfaces and link state
ip route show     # Show routing table
hostname -I       # Show IP addresses
netstat -rn       # Routing table (legacy)
```

#### **Connectivity Testing**

```bash
ping <host>       # Test connectivity
traceroute <host> # Trace path to host
curl -I <url>     # Check HTTP response headers
```

#### **Port & Service Checks**

```bash
ss -tulnp        # Show listening TCP/UDP ports
netstat -tulnp    # Alternative legacy command
lsof -i :<port>   # Find which process is using a port
```

#### **Network Performance & Traffic**

```bash
ifconfig          # Legacy: show interface info
ethtool eth0      # Show link speed and NIC info
mtr <host>        # Continuous traceroute + ping (real-time)
tcpdump -i eth0   # Capture network packets
nmap <host>       # Scan open ports
```

#### **DNS & Host Resolution**

```bash
dig example.com
nslookup example.com
host example.com
```

#### **Firewall / Connectivity Check**

```bash
iptables -L -n -v   # Legacy firewall rules
firewall-cmd --list-all # firewalld (RHEL7/8/9)
```

---

### 2️⃣ Real-Time Production Issues / Scenarios

1. **Network Interface Down**
    
    * Symptom: Users cannot access services
        
    * Diagnosis: `ip a` → interface is `DOWN`
        
    * Fix: `ip link set eth0 up`, check cables/NICs
        
2. **DNS Resolution Failures**
    
    * Symptom: Servers cannot resolve hostnames
        
    * Diagnosis: `dig +trace` [`example.com`](http://example.com) → check upstream DNS
        
    * Fix: Update `/etc/resolv.conf`, check firewall rules
        
3. **High Latency / Packet Loss**
    
    * Symptom: Slow application response
        
    * Diagnosis: `ping` or `mtr` → check latency or packet loss
        
    * Fix: Investigate network path, QoS issues, or ISP routing
        
4. **Port Conflict**
    
    * Symptom: Service fails to start
        
    * Diagnosis: `ss -tulnp | grep <port>` → identify process using port
        
    * Fix: Kill conflicting process or change service port
        
5. **Firewall Blocking Traffic**
    
    * Symptom: Services not reachable externally
        
    * Diagnosis: `iptables -L -n -v` or `firewall-cmd --list-all`
        
    * Fix: Add proper rules, allow traffic to required ports
        
6. **Misconfigured Routing**
    
    * Symptom: Cannot reach remote subnet
        
    * Diagnosis: `ip route` → check default route and subnet routes
        
    * Fix: Add missing route: `ip route add <subnet> via <gateway>`
        
7. **High Network I/O on Server**
    
    * Symptom: Server slow, high NIC utilization
        
    * Diagnosis: `iftop -i eth0` or `nload`
        
    * Fix: Identify process generating traffic, optimize, or offload
        

---

## **Topic 3: Networking Commands — Interview Questions**

| Question | Answer / Approach |
| --- | --- |
| How do you quickly see all interfaces and IPs in a readable way? | `ip -br addr` — brief, compact output |
| How do you test connectivity to a host? | `ping <host>` or `mtr <host>` for continuous trace |
| How do you check which process is listening on a specific port? | \`ss -tulnp |
| How do you troubleshoot DNS issues on a server? | `dig +trace` [`example.com`](http://example.com), check `/etc/resolv.conf`, use `nslookup` |
| How do you check routing issues? | `ip route` or `netstat -rn` |
| How to monitor real-time network traffic per process? | `iftop -i eth0`, `nload`, or `tcpdump -i eth0` |
| How to verify firewall rules? | `iptables -L -n -v` or `firewall-cmd --list-all` |
| How do you identify network latency or packet loss? | `ping -c 10 <host>` or `mtr <host>` |

---

## **Topic 4: Networking Commands — Hands-On Tasks**

1. **Check interfaces and IPs (brief & full)**
    

```bash
ip a
ip -br addr
```

2. **Check routing table**
    

```bash
ip route show
netstat -rn
```

3. **Test connectivity to Google**
    

```bash
ping -c 4 8.8.8.8
traceroute 8.8.8.8
```

4. **Check open ports and services**
    

```bash
ss -tulnp
lsof -i :80
```

5. **Check real-time network traffic**
    

```bash
iftop -i eth0
nload
```

6. **Check DNS resolution**
    

```bash
dig google.com
nslookup google.com
```

7. **Verify firewall rules**
    

```bash
iptables -L -n -v
firewall-cmd --list-all
```

8. **Simulate latency or packet loss testing**
    

```bash
ping -c 10 google.com
mtr google.com
```

---

✅ **Outcome:**  
After completing these tasks, you will be able to:

* Quickly identify network connectivity and performance issues.
    
* Monitor ports, services, and real-time traffic.
    
* Troubleshoot DNS, routing, and firewall problems.
    
* Confidently explain approaches in interviews.
    

---

## **Topic 5: Process & Service Management (Advanced)**

### 1️⃣ Commands

#### **Process Listing & Monitoring**

```bash
ps -ef                 # List all processes with full details
ps aux --sort=-%cpu     # Sort by CPU usage
top                     # Real-time process monitoring
htop                    # Interactive process viewer
pidof <process>         # Get PID by process name
pgrep <pattern>         # Search for process by pattern
jobs                    # List background jobs
```

#### **Process Management**

```bash
kill <PID>              # Graceful termination
kill -9 <PID>           # Force kill
pkill <process_name>    # Kill by process name
nice -n 10 ./script.sh  # Start process with lower priority
renice -n -5 -p <PID>   # Change priority of running process
nohup ./script.sh &     # Run process immune to logout
fg %<job_number>        # Bring background job to foreground
bg %<job_number>        # Send stopped job to background
```

#### **Service Management (systemd)**

```bash
systemctl status nginx          # Check service status
systemctl start nginx           # Start service
systemctl stop nginx            # Stop service
systemctl restart nginx         # Restart service
systemctl enable nginx          # Enable at boot
systemctl disable nginx         # Disable at boot
journalctl -u nginx -f          # View service logs in real-time
```

#### **Debugging / Tracing Processes**

```bash
lsof -p <PID>                   # List open files & network sockets
strace -p <PID>                 # Trace system calls of process
pstree -p                       # Show process tree with PIDs
```

---

### 2️⃣ Real-Time Production Issues / Scenarios

1. **CPU/Memory Spike**
    
    * Symptom: Application becomes slow
        
    * Diagnosis: `top` → check top CPU/Memory processes
        
    * Fix: `renice` critical processes, investigate batch jobs
        
2. **Zombie Processes**
    
    * Symptom: Hundreds of `<defunct>` processes
        
    * Cause: Parent process didn’t call `wait()`
        
    * Fix: Restart parent service, investigate script behavior
        
3. **Stuck or Hung Service**
    
    * Symptom: Service not responding
        
    * Diagnosis: `systemctl status <service>` shows `failed` or `activating`
        
    * Fix: `journalctl -u <service>` → identify logs, restart service
        
4. **Background Job Interruption**
    
    * Symptom: Long-running script killed after SSH logout
        
    * Cause: Not running with `nohup` or inside `screen/tmux`
        
    * Fix: Use `nohup ./`[`script.sh`](http://script.sh) `&` or run in `screen` session
        
5. **Port Conflict on Service Start**
    
    * Symptom: Service fails to start
        
    * Diagnosis: `ss -tulnp | grep <port>`
        
    * Fix: Kill conflicting process or change service port
        
6. **Process Ownership / Permission Issue**
    
    * Symptom: Service cannot bind to port &lt;1024
        
    * Cause: Running as non-root
        
    * Fix: Change user to root in service unit file or use `setcap`
        

---

## **Topic 5: Process & Service Management — Interview Questions**

| Question | Answer / Approach |
| --- | --- |
| How do you check all running processes? | `ps -ef` or `top` for real-time view |
| How do you find which process is using most CPU? | `top` → sort by `%CPU` or `ps aux --sort=-%cpu` |
| How do you kill a process safely? | `kill <PID>` → graceful, `kill -9 <PID>` → forceful |
| How do you change process priority? | `nice -n 10 <command>` to start, `renice -n -5 -p <PID>` for running |
| How do you run a long script without it being killed after logout? | `nohup ./`[`script.sh`](http://script.sh) `&` or use `screen`/`tmux` |
| How do you check service status and logs? | `systemctl status <service>` + `journalctl -u <service> -f` |
| How do you enable a service to start at boot? | `systemctl enable <service>` |
| How do you debug a hung process? | `strace -p <PID>`, `lsof -p <PID>`, check logs via `journalctl` |
| How do you identify zombie processes? | \`ps aux |
| How do you fix a port conflict for a service? | \`ss -tulnp |

---

## **Topic 5: Process & Service Management — Hands-On Tasks**

1. **List all running processes and sort by CPU**
    

```bash
ps aux --sort=-%cpu | head -10
top
htop
```

2. **Find and kill a specific process**
    

```bash
pidof myscript.sh
kill <PID>
kill -9 <PID>  # if unresponsive
```

3. **Run a script in the background safely**
    

```bash
nohup ./longtask.sh &
jobs
fg %1
bg %1
```

4. **Change process priority**
    

```bash
nice -n 10 ./script.sh
renice -n -5 -p <PID>
```

5. **Service management with systemd**
    

```bash
systemctl status nginx
systemctl restart nginx
systemctl enable nginx
journalctl -u nginx -f
```

6. **Debug a stuck process**
    

```bash
strace -p <PID>
lsof -p <PID>
pstree -p
```

7. **Check for zombie processes**
    

```bash
ps aux | grep defunct
top   # look for <defunct>
```

8. **Identify port conflicts**
    

```bash
ss -tulnp | grep 80
lsof -i :80
```

---

✅ **Outcome:**  
After this topic, you will be able to:

* Monitor, manage, and debug processes in production
    
* Handle long-running scripts and service issues
    
* Fix zombie processes, port conflicts, and hung services
    
* Explain process/service management confidently in interviews
    

---

## **Topic 5: User & Permission Management (Enterprise / Real-Time)**

### 1️⃣ Commands + Real-Time Production Issues

#### **Local User Management (for reference)**

```bash
useradd <username>           # Add local user
usermod -aG <group> <user>  # Add local user to group
userdel -r <username>        # Delete local user and home directory
id <username>                # Show UID, GID, groups
groups <username>            # List groups
passwd <username>            # Set/change password
chage -l <username>          # Check password expiry
```

#### **SSSD / FreeIPA / LDAP User Management**

```bash
# Check if SSSD is running
systemctl status sssd

# List users from FreeIPA / LDAP
getent passwd | grep <username>
id <username>             # Verify UID/GID and groups from centralized system

# Check groups
getent group | grep <groupname>

# Test authentication for user via PAM
su - <username>
```

---

### **Real-Time Production Issues**

1. **SSSD / FreeIPA Authentication Failure**
    
    * Symptom: Users cannot login to server
        
    * Diagnosis: `systemctl status sssd`, `journalctl -u sssd`, `getent passwd <user>`
        
    * Causes: SSSD cache corrupted, FreeIPA server down, expired tickets
        
    * Fix: `sss_cache -E` to clear cache, restart `sssd`, check network connectivity to FreeIPA/Kerberos
        
2. **Group Membership Not Applied**
    
    * Symptom: User cannot access certain files or services despite being in FreeIPA group
        
    * Diagnosis: `id <username>` shows missing group locally (cached)
        
    * Fix: `sss_cache -U <username>` → refresh SSSD cache
        
3. **Password Expiry / Expired Ticket**
    
    * Symptom: `Authentication failure` on login
        
    * Diagnosis: `chage -l <username>` (for local) or `ipa user-show <username>`
        
    * Fix: Reset password in FreeIPA or renew Kerberos ticket
        
4. **Offline / Cache Mode Issues**
    
    * Symptom: Server cannot authenticate when FreeIPA / LDAP unreachable
        
    * Diagnosis: Check `/etc/sssd/sssd.conf` and offline cache validity
        
    * Fix: Wait for SSSD cache refresh or bring FreeIPA/LDAP online
        
5. **Unauthorized Access / Privilege Escalation**
    
    * Symptom: User in LDAP gains sudo or root access unexpectedly
        
    * Diagnosis: Check FreeIPA role memberships and `/etc/sudoers.d/`
        
    * Fix: Remove user from privileged groups, update FreeIPA roles
        
6. **SSSD Service Not Running**
    
    * Symptom: All domain logins fail
        
    * Diagnosis: `systemctl status sssd` shows inactive or failed
        
    * Fix: `systemctl restart sssd`, check logs in `/var/log/sssd/`
        

---

✅ This is **the real-world production view** of user and permission management, covering:

* Local users
    
* Centralized authentication (SSSD / FreeIPA / LDAP)
    
* Caching issues, group membership, and password problems
    
* Kerberos/SSSD-related troubleshooting
    

---

## **Topic 5: User & Permission Management — Interview Questions**

| Question | Answer / Approach |
| --- | --- |
| How do you check a user’s groups? | `id <username>` or `groups <username>` |
| How do you add a local user to a group? | `usermod -aG <group> <username>` |
| How do you delete a local user? | `userdel -r <username>` |
| How do you check if SSSD is running? | `systemctl status sssd` |
| How do you list users from FreeIPA/LDAP? | `getent passwd <username>` |
| How do you refresh SSSD cache? | `sss_cache -U <username>` or `sss_cache -E` for all |
| How to troubleshoot login failure from FreeIPA? | Check `journalctl -u sssd`, network connectivity, Kerberos tickets (`klist`) |
| How to check password expiry for local and FreeIPA users? | Local: `chage -l <username>` / FreeIPA: `ipa user-show <username>` |
| How to detect unauthorized sudo access? | `sudo -l -U <username>` and check `/etc/sudoers` + FreeIPA roles |
| How to troubleshoot offline authentication? | Check `/etc/sssd/sssd.conf`, SSSD cache validity, restart `sssd` service |

---

## **Hands-On Tasks**

1. **Check a user’s groups (local + centralized)**
    

```bash
id revuser
groups revuser
getent passwd revuser
```

2. **Add user to group locally**
    

```bash
usermod -aG developers revuser
id revuser
```

3. **Check SSSD status and logs**
    

```bash
systemctl status sssd
journalctl -u sssd -f
```

4. **Refresh SSSD cache**
    

```bash
sss_cache -U revuser   # single user
sss_cache -E           # all users
```

5. **Test centralized login**
    

```bash
su - revuser
klist                   # check Kerberos ticket
```

6. **Check password expiry**
    

```bash
chage -l revuser         # local
ipa user-show revuser    # FreeIPA
```

7. **Simulate login failure / cache issue**
    

```bash
sss_cache -E             # clear cache
su - revuser             # test login again
```

---

### **Brief Notes on Centralized Authentication & Troubleshooting**

* **Centralized authentication**: FreeIPA / LDAP / AD via SSSD
    
    * Users, groups, and sudo roles are managed centrally
        
    * Servers authenticate via Kerberos tickets
        
* **Caching issues**:
    
    * SSSD caches user/group info locally
        
    * Cache might cause outdated group membership or login failures
        
    * Fix: `sss_cache -U <user>` or `sss_cache -E`
        
* **Group membership & sudo problems**:
    
    * Sometimes a user appears in FreeIPA but cannot access files or sudo
        
    * Fix: Refresh SSSD cache, verify sudo roles and group membership
        
* **Kerberos / SSSD login issues**:
    
    * Common causes: expired ticket, clock skew, SSSD not running, network down
        
    * Fix: `klist`, sync time, restart `sssd`, check `/var/log/sssd/`
        

---

## **Topic 6: System Logs & Monitoring**

### 1️⃣ Commands + Real-Time Production Issues

#### **Commands**

**View Logs**

```bash
journalctl                 # View all systemd logs
journalctl -u <service>    # Logs for a specific service
journalctl -b               # Logs from current boot
journalctl -f               # Follow logs in real-time
journalctl --since "1 hour ago"

# Filter by priority
journalctl -p err           # Show only errors
journalctl -p warning       # Show warnings
```

**Rsyslog (Legacy)**

```bash
cat /var/log/messages        # General system log
cat /var/log/secure          # Authentication / sudo logs
cat /var/log/cron            # Cron job logs
cat /var/log/httpd/error_log # Web server logs
```

**Monitoring Tools**

```bash
top                         # Real-time CPU/Memory monitoring
htop                        # Interactive process monitor
vmstat 2 5                  # CPU, memory, swap stats every 2 sec, 5 times
iostat -xz 2 3              # Disk I/O stats
sar -u 2 3                  # CPU utilization over time
```

---

#### **Real-Time Production Issues**

1. **Service Crashes**
    
    * Symptom: Service stops unexpectedly
        
    * Diagnosis: `journalctl -u <service> -f`, check error logs in `/var/log/`
        
    * Fix: Investigate error, restart service, check config
        
2. **High CPU / Memory**
    
    * Symptom: Server slow
        
    * Diagnosis: `top`, `vmstat`, `sar` → identify bottleneck
        
    * Fix: Kill rogue processes, optimize workload
        
3. **Disk Full / Log Overflow**
    
    * Symptom: `/var/log` fills up, services fail
        
    * Diagnosis: `du -sh /var/log/*`, check `rsyslog` config
        
    * Fix: Rotate logs with `logrotate`, archive old logs
        
4. **Missing Logs**
    
    * Symptom: Logs not appearing for certain service
        
    * Diagnosis: Check `rsyslog` config `/etc/rsyslog.conf` or service-specific config
        
    * Fix: Correct log file path, restart `rsyslog`
        
5. **Delayed / Out-of-Order Logs**
    
    * Symptom: Logs appear late or mixed
        
    * Cause: Clock skew, buffer issues in rsyslog
        
    * Fix: Sync time (`chronyd` / `ntpd`), restart logging service
        

---

## **Topic 6: System Logs & Monitoring — Interview Questions**

| Question | Answer / Approach |
| --- | --- |
| How do you check logs for a specific service? | `journalctl -u <service>` |
| How do you follow logs in real-time? | `journalctl -f` |
| How do you see logs only from the current boot? | `journalctl -b` |
| How do you filter logs by priority? | `journalctl -p err` or `journalctl -p warning` |
| How do you view old logs from `/var/log/messages` or `/var/log/secure`? | `cat /var/log/messages` or `less /var/log/messages` |
| How do you monitor CPU, memory, and I/O in real-time? | `top`, `htop`, `vmstat 2 5`, `iostat -xz 2 3`, `sar -u 2 3` |
| What to do if logs are missing for a service? | Check service-specific logging config and rsyslog (`/etc/rsyslog.conf`), restart rsyslog |
| How to troubleshoot delayed or out-of-order logs? | Check system time (`chronyd`/`ntpd`), check rsyslog buffers, restart logging service |
| How to rotate large log files? | Configure `logrotate`, manually `logrotate -f /etc/logrotate.conf` |
| How to view authentication logs? | `cat /var/log/secure` or `journalctl _COMM=sshd` |

---

## **Topic 6: System Logs & Monitoring — Hands-On Tasks**

1. **View logs for a service**
    

```bash
journalctl -u nginx
journalctl -u sshd -f
```

2. **View logs from current boot**
    

```bash
journalctl -b
```

3. **Filter logs by priority**
    

```bash
journalctl -p err
journalctl -p warning --since "2 hours ago"
```

4. **Monitor CPU, memory, and I/O**
    

```bash
top
htop
vmstat 2 5
iostat -xz 2 3
sar -u 2 3
```

5. **Check general system logs**
    

```bash
cat /var/log/messages | tail -20
cat /var/log/secure | tail -20
cat /var/log/cron | tail -20
```

6. **Simulate high CPU and monitor**
    

```bash
# Run CPU stress in background
stress --cpu 2 --timeout 60

# Monitor usage
top
vmstat 2 5
```

7. **Check for missing logs**
    

```bash
ls -l /var/log/
journalctl -u customservice
systemctl status rsyslog
```

8. **Rotate logs manually**
    

```bash
logrotate -f /etc/logrotate.conf
```

---

✅ **Outcome:**  
After this topic, you will be able to:

* Analyze system and service logs efficiently
    
* Monitor server health and performance in real-time
    
* Troubleshoot production issues like service crashes, high CPU/memory, disk full, and missing logs
    
* Confidently answer interview questions on logging and monitoring
    

---

## **Topic 7: Package & Service Management (Deep Dive with YUM)**

### 1️⃣ Commands + Real-Time Production Issues

#### **YUM Basics & Repos**

```bash
# List all enabled repositories
yum repolist

# List all available packages from all repos
yum list all

# Check if updates are available
yum check-update

# Search for a specific package
yum search <package_name>

# Show info about installed package
yum info <package_name>

# Install / Update / Remove package
yum install <package_name>
yum update <package_name>
yum remove <package_name>

# Clean YUM cache
yum clean all
yum makecache
```

#### **YUM Repo Configuration**

```bash
# Repository files are in /etc/yum.repos.d/
ls /etc/yum.repos.d/

# Check enabled/disabled in a repo file
cat /etc/yum.repos.d/<repo_file>.repo

# Temporarily enable or disable repo
yum --enablerepo=<repo> install <package>
yum --disablerepo=<repo> update
```

#### **Verify Package Integrity**

```bash
rpm -V <package_name>        # Verify files installed by package
rpm -qa | grep <package>     # Check if installed
rpm -ql <package_name>       # List all files installed
```

---

### **Service Management (systemd)**

```bash
systemctl status <service>
systemctl start <service>
systemctl stop <service>
systemctl restart <service>
systemctl reload <service>
systemctl enable <service>
systemctl disable <service>
systemctl is-active <service>
journalctl -u <service>
```

---

### **Real-Time Production Issues with YUM & Services**

1. **Failed Package Install**
    
    * Symptom: `Error: Package not found`
        
    * Diagnosis: Check repo availability `yum repolist` and network connectivity
        
    * Fix: Enable correct repo or add missing repo
        
2. **Dependency Conflicts**
    
    * Symptom: `Error: Package X requires Y`
        
    * Diagnosis: `yum deplist <package>` → shows missing dependencies
        
    * Fix: Install missing dependencies manually or fix broken repo
        
3. **Cache Issues**
    
    * Symptom: `yum check-update` not showing latest packages
        
    * Fix: `yum clean all && yum makecache`
        
4. **Service Fails After Package Update**
    
    * Symptom: Application/service not working
        
    * Diagnosis: `journalctl -u <service>` → config changes, missing files
        
    * Fix: Fix configuration or rollback to previous package version
        
5. **Repo Misconfiguration**
    
    * Symptom: Packages not found or pointing to wrong repo
        
    * Diagnosis: `cat /etc/yum.repos.d/<repo>.repo`
        
    * Fix: Correct baseurl, gpgkey, or enable/disable repo
        

---

### **Additional Real-Time Production Issues:** `/var` Full & YUM / Service Failures

1. **Disk Full (/var) due to Package / Log Files**
    
    * **Symptom:** `yum install` or `yum update` fails with `No space left on device`, services fail to start, logs cannot be written.
        
    * **Diagnosis:**
        
        ```bash
        df -h /var
        du -sh /var/cache/yum/*       # Check YUM cache usage
        du -sh /var/log/*              # Check logs usage
        ```
        
    * **Fix / Mitigation:**
        
        ```bash
        yum clean all                   # Clear YUM cache
        logrotate -f /etc/logrotate.conf # Rotate logs manually
        rm -rf /var/cache/yum/*         # Free up space
        ```
        
    * **Best Practice:** Configure `logrotate`, monitor `/var` usage, and avoid keeping old packages cached indefinitely.
        
2. **Service Fails After Update / Disk Full**
    
    * **Symptom:** `systemctl start <service>` fails; journal logs show disk write errors
        
    * **Diagnosis:** `journalctl -xe` shows “No space left on device”
        
    * **Fix:** Free up `/var` (clean yum cache, rotate logs), restart service:
        
        ```bash
        systemctl restart <service>
        ```
        
3. **Large Package Downloads Filling Disk**
    
    * **Symptom:** `yum install` downloads multiple GBs in `/var/cache/yum`
        
    * **Fix:** Clean cache after install:
        
        ```bash
        yum clean packages
        yum clean all
        ```
        

---

✅ **Outcome:**  
Now for **production-level package & service management**, you know:

* YUM repo handling & troubleshooting
    
* Dependency issues & cache management
    
* Disk space (/var) issues affecting YUM and services
    
* Service start/restart failures post-update
    

---

### **Additional Production Issues to Include**

1. **GPG Key / Repository Verification Failure**
    
    * **Symptom:** `GPG key is not installed` or `Failed to download metadata`
        
    * **Diagnosis:** `yum repolist`, check `.repo` file for `gpgkey` URL
        
    * **Fix:** Import the repo GPG key:
        
        ```bash
        rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-<repo>
        yum clean all
        yum makecache
        ```
        
2. **Locked RPM Database**
    
    * **Symptom:** `Another app is currently holding the yum lock`
        
    * **Diagnosis:** Check `/var/lib/rpm/.rpm.lock` or running process
        
    * **Fix:** Kill stale process or remove lock file carefully:
        
        ```bash
        rm -f /var/lib/rpm/.rpm.lock
        yum clean all
        ```
        
3. **Corrupted RPM Database**
    
    * **Symptom:** `rpmdb: Unexpected EOF` or `RPM database is corrupt`
        
    * **Fix:**
        
        ```bash
        rpm --rebuilddb
        yum clean all
        ```
        
4. **Service Failure due to SELinux**
    
    * **Symptom:** Service fails after package install, logs show permission denied
        
    * **Fix:** Check SELinux status:
        
        ```bash
        sestatus
        ausearch -m avc -ts recent
        setenforce 0  # temporary troubleshoot
        ```
        
5. **Multiple Versions / Orphaned Packages**
    
    * **Symptom:** Old packages occupy space, conflicts with new versions
        
    * **Fix:** List installed packages and remove old versions:
        
        ```bash
        rpm -qa | grep <package>
        yum remove <old_package_version>
        ```
        
6. **Repository Down / Network Issues**
    
    * **Symptom:** `Could not retrieve mirror` or `Timeout`
        
    * **Fix:** Check network, switch mirrors, or use cached packages with `yum localinstall`
        

---

## **Topic 7: Package & Service Management — Interview Questions**

| Question | Answer / Approach |
| --- | --- |
| How do you check if a package is installed? | \`rpm -qa |
| How do you view files installed by a package? | `rpm -ql <package>` |
| How do you verify package integrity? | `rpm -V <package>` |
| How do you list all packages available from repos? | `yum list all` |
| How do you check for available updates? | `yum check-update` |
| How do you install, update, or remove a package? | `yum install <package>` / `yum update <package>` / `yum remove <package>` |
| How do you troubleshoot a package install failure? | Check repo: `yum repolist`, network, dependency issues `yum deplist <package>` |
| How do you fix YUM cache or metadata issues? | `yum clean all` → `yum makecache` |
| How do you handle a locked RPM database? | Remove stale lock: `rm -f /var/lib/rpm/.rpm.lock` and rebuild database: `rpm --rebuilddb` |
| How do you handle service failures after package updates? | Check logs: `journalctl -u <service>`, restart: `systemctl restart <service>` |

**Production-specific Questions**

| Question | Answer / Approach |
| --- | --- |
| Disk `/var` full due to YUM | Clean cache: `yum clean all`, rotate logs, remove old packages |
| GPG key / repo verification fails | Import key: `rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-<repo>` |
| Multiple versions / orphaned packages | \`rpm -qa |
| Service failure due to SELinux | Check `sestatus`, `ausearch -m avc -ts recent` → troubleshoot context issues |
| Repo unreachable / network issue | Check connectivity, switch mirrors, use cached packages |

---

## **Hands-On Tasks**

1. **Check installed packages**
    

```bash
rpm -qa | grep httpd
rpm -ql httpd
rpm -V httpd
```

2. **Search, install, update, and remove packages**
    

```bash
yum search nginx
yum install nginx
yum update nginx
yum remove nginx
```

3. **Check YUM updates and repo list**
    

```bash
yum check-update
yum repolist
yum list all
```

4. **Clean YUM cache**
    

```bash
yum clean packages
yum clean all
yum makecache
```

5. **Rebuild RPM database if corrupted**
    

```bash
rm -f /var/lib/rpm/__db*
rpm --rebuilddb
yum clean all
```

6. **Check and manage systemd service**
    

```bash
systemctl status nginx
systemctl restart nginx
systemctl enable nginx
journalctl -u nginx -f
```

7. **Simulate /var full due to cache**
    

```bash
du -sh /var/cache/yum/*
yum clean all
```

8. **GPG / Repo troubleshooting**
    

```bash
yum clean all
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
yum makecache
```

9. **Check for orphaned packages**
    

```bash
rpm -qa | grep oldpkg
yum remove oldpkg-version
```

---

✅ **Outcome:**  
After completing this topic, you will be able to:

* Fully manage YUM repositories and packages
    
* Troubleshoot package installation and dependency issues
    
* Handle disk space issues affecting package management
    
* Manage and troubleshoot systemd services post-update
    
* Explain and answer production-related questions confidently in interviews
    

---

## **Topic 8: SELinux & Firewall Basics**

### 1️⃣ Commands + Real-Time Production Issues

#### **SELinux Commands**

```bash
sestatus                     # Check SELinux status
getenforce                    # Show current mode (Enforcing / Permissive / Disabled)
setenforce 0                  # Temporarily set to Permissive
setenforce 1                  # Temporarily set to Enforcing
semanage port -l              # List SELinux port contexts
ausearch -m avc -ts today    # Search audit logs for AVC denials
audit2allow -w -a             # Suggests why SELinux denied access
audit2allow -M mymodule       # Generate policy module to allow action
```

#### **Firewall Commands (firewalld)**

```bash
firewall-cmd --state                 # Check if firewall is running
firewall-cmd --list-all              # Show active zones and rules
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --reload
firewall-cmd --zone=public --remove-port=80/tcp --permanent
firewall-cmd --list-ports
```

---

### **Real-Time Production Issues**

1. **Service Fails Due to SELinux**
    
    * Symptom: Application cannot bind port, write to file, or access directories
        
    * Diagnosis: `ausearch -m avc -ts today` → `journalctl` shows SELinux denial
        
    * Fix: Temporarily set permissive `setenforce 0`, or create policy module:
        
        ```bash
        audit2allow -M mymodule
        semodule -i mymodule.pp
        ```
        
2. **Firewall Blocking Access**
    
    * Symptom: Remote access or HTTP/HTTPS fails
        
    * Diagnosis: `firewall-cmd --list-all` → check allowed ports
        
    * Fix: Open required ports and reload firewall
        
        ```bash
        firewall-cmd --zone=public --add-port=443/tcp --permanent
        firewall-cmd --reload
        ```
        
3. **Temporary Changes Lost After Reboot**
    
    * Symptom: Firewall rules or SELinux mode reset
        
    * Diagnosis: Check if `--permanent` flag used with `firewall-cmd`
        
    * Fix: Always use `--permanent` for persistent changes; for SELinux, edit `/etc/selinux/config`
        
4. **SELinux File Context Misconfiguration**
    
    * Symptom: Web server cannot access files
        
    * Diagnosis: `ls -Z /var/www/html` → check context
        
    * Fix: Restore default context:
        
        ```bash
        restorecon -Rv /var/www/html
        ```
        
5. **SELinux Audit Log Volume**
    
    * Symptom: `/var/log/audit/audit.log` grows fast
        
    * Fix: Rotate audit logs, monitor AVC denials, and adjust policies as needed
        

---

## **Topic 8: SELinux & Firewall Basics — Interview Questions**

| Question | Answer / Approach |
| --- | --- |
| How do you check the current SELinux mode? | `getenforce` or `sestatus` |
| How do you temporarily set SELinux to permissive mode? | `setenforce 0` |
| How do you permanently change SELinux mode? | Edit `/etc/selinux/config` → set `SELINUX=enforcing/permissive/disabled` |
| How do you find SELinux denials in logs? | `ausearch -m avc -ts today` or `journalctl -t setroubleshoot` |
| How do you generate a policy module to allow denied access? | `audit2allow -M mymodule` → `semodule -i mymodule.pp` |
| How do you check if the firewall is running? | `firewall-cmd --state` |
| How do you list all active firewall zones and rules? | `firewall-cmd --list-all` |
| How do you permanently open a port in the firewall? | `firewall-cmd --zone=public --add-port=80/tcp --permanent` → `firewall-cmd --reload` |
| How do you restore SELinux file contexts for a directory? | `restorecon -Rv /path/to/directory` |
| What do you do if firewall rules or SELinux mode resets after reboot? | Use `--permanent` for firewall, edit `/etc/selinux/config` for SELinux |

---

## **Hands-On Tasks**

1. **Check SELinux status and current mode**
    

```bash
sestatus
getenforce
```

2. **Temporarily set SELinux permissive**
    

```bash
setenforce 0
getenforce
```

3. **Check for recent SELinux denials**
    

```bash
ausearch -m avc -ts today
audit2allow -w -a   # Suggested solutions for denials
```

4. **Generate and install SELinux policy module**
    

```bash
audit2allow -M mymodule
semodule -i mymodule.pp
```

5. **Check firewall status and active rules**
    

```bash
firewall-cmd --state
firewall-cmd --list-all
```

6. **Open a port (HTTP/HTTPS) permanently**
    

```bash
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=443/tcp --permanent
firewall-cmd --reload
firewall-cmd --list-ports
```

7. **Remove a port from firewall**
    

```bash
firewall-cmd --zone=public --remove-port=80/tcp --permanent
firewall-cmd --reload
firewall-cmd --list-ports
```

8. **Restore default SELinux context for web server**
    

```bash
ls -Z /var/www/html
restorecon -Rv /var/www/html
ls -Z /var/www/html
```

---

✅ **Outcome:**  
After this topic, you will be able to:

* Troubleshoot SELinux denials and apply policies
    
* Manage firewall rules in production (open/close ports, zones, persistence)
    
* Handle common production issues like service denial, lost rules, or misconfigured contexts
    
* Answer interview questions confidently on SELinux and firewall basics
    

---

## **Topic 9: Crontab & Scheduled Jobs**

### 1️⃣ Commands + Real-Time Production Issues

#### **Crontab Basics**

```bash
# View user’s cron jobs
crontab -l

# Edit user’s cron jobs
crontab -e

# Remove user’s cron jobs
crontab -r

# List system-wide cron jobs
ls /etc/cron.d/
ls /etc/cron.daily/
ls /etc/cron.hourly/
ls /etc/cron.weekly/
ls /etc/cron.monthly/
```

#### **Cron Job Syntax**

```bash
* * * * * command_to_run
- - - - -
| | | | |
| | | | +---- Day of week (0-7) (Sunday=0 or 7)
| | | +------ Month (1-12)
| | +-------- Day of month (1-31)
| +---------- Hour (0-23)
+------------ Minute (0-59)
```

#### **Common Cron Commands**

```bash
# Redirect output to file
* * * * * /path/to/script.sh >> /var/log/script.log 2>&1

# Check cron service status
systemctl status crond

# Restart cron service
systemctl restart crond
```

---

### **Real-Time Production Issues**

1. **Cron Job Not Executing**
    
    * Symptom: Script doesn’t run at scheduled time
        
    * Diagnosis: Check cron logs `/var/log/cron`, ensure `crond` is running
        
    * Fix: Start/restart cron, check script permissions, correct syntax
        
2. **Environment Variables Missing**
    
    * Symptom: Scripts fail under cron but work manually
        
    * Fix: Explicitly define environment variables in crontab or script
        
        ```bash
        PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
        ```
        
3. **Output Not Captured**
    
    * Symptom: Errors or output not logged
        
    * Fix: Redirect stdout and stderr in crontab:
        
        ```bash
        * * * * * /path/to/script.sh >> /var/log/script.log 2>&1
        ```
        
4. **Multiple Instances Running**
    
    * Symptom: Script overlaps itself, causing errors
        
    * Fix: Use `flock` or check running process in script:
        
        ```bash
        flock -n /tmp/myscript.lock /path/to/script.sh
        ```
        
5. **Cron Service Down**
    
    * Symptom: None of the jobs execute
        
    * Fix:
        
        ```bash
        systemctl status crond
        systemctl start crond
        systemctl enable crond
        ```
        
6. **Permissions Issues**
    
    * Symptom: Cron job fails to read/write files
        
    * Fix: Check script ownership and permissions; ensure user has access
        

---

## **Topic 9: Crontab & Scheduled Jobs — Interview Questions**

| Question | Answer / Approach |
| --- | --- |
| How do you list a user’s cron jobs? | `crontab -l` |
| How do you edit a user’s cron jobs? | `crontab -e` |
| How do you remove all cron jobs for a user? | `crontab -r` |
| How do you check system-wide cron jobs? | `/etc/cron.d/`, `/etc/cron.daily/`, `/etc/cron.hourly/` |
| How do you check if the cron service is running? | `systemctl status crond` |
| How do you redirect cron job output to a log file? | `* * * * * /path/`[`script.sh`](http://script.sh) `>> /var/log/script.log 2>&1` |
| How do you troubleshoot a cron job that isn’t executing? | Check `/var/log/cron`, verify script permissions, ensure `crond` service is running |
| How do you prevent multiple instances of a cron job? | Use `flock` or a PID lock inside the script |
| Why does a cron job succeed manually but fail in crontab? | Environment variables missing (`PATH`, `HOME`, etc.) — define them in crontab or script |
| How do you schedule a cron job to run every day at 2:30 AM? | `30 2 * * * /path/to/`[`script.sh`](http://script.sh) |

---

## **Hands-On Tasks**

1. **List and edit cron jobs**
    

```bash
crontab -l
crontab -e
```

2. **Schedule a simple cron job**
    

```bash
# Run a script every day at 2:30 AM
30 2 * * * /home/user/backup.sh >> /var/log/backup.log 2>&1
```

3. **Check system-wide cron jobs**
    

```bash
ls /etc/cron.d/
ls /etc/cron.daily/
ls /etc/cron.hourly/
```

4. **Redirect cron output**
    

```bash
* * * * * /home/user/test.sh >> /var/log/test.log 2>&1
```

5. **Prevent multiple instances**
    

```bash
# Using flock
* * * * * flock -n /tmp/test.lock /home/user/test.sh
```

6. **Verify cron service**
    

```bash
systemctl status crond
systemctl restart crond
systemctl enable crond
```

7. **Simulate environment issue**
    

```bash
# Script works manually but fails in cron
echo $PATH >> /tmp/path.log
# Fix by adding PATH in crontab
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

---

✅ **Outcome:**  
After this topic, you will be able to:

* Schedule and manage cron jobs effectively
    
* Troubleshoot common cron issues like missing environment variables, output redirection, multiple instances, and service failure
    
* Answer interview questions confidently on cron & scheduled jobs
    

---

## **Topic 10: Boot Process & Runlevels (targets)**

### 1️⃣ Commands + Real-Time Production Issues

#### **Understanding Runlevels / Targets**

| Runlevel (SysV) | Description | Systemd Target (RHEL 7/8/9) |
| --- | --- | --- |
| 0 | Halt / Power off | [`poweroff.target`](http://poweroff.target) |
| 1 | Single user mode | [`rescue.target`](http://rescue.target) |
| 3 | Multi-user, text mode | [`multi-user.target`](http://multi-user.target) |
| 5 | Multi-user, GUI | [`graphical.target`](http://graphical.target) |
| 6 | Reboot | [`reboot.target`](http://reboot.target) |

#### **Systemd Commands**

```bash
# Check current target
systemctl get-default

# List all available targets
systemctl list-units --type=target

# Change target temporarily (no reboot)
systemctl isolate multi-user.target

# Change target permanently
systemctl set-default multi-user.target

# Check boot logs
journalctl -b

# Check failed services at boot
systemctl --failed

# Show dependencies for a target
systemctl list-dependencies graphical.target
```

#### **Kernel & Boot Info**

```bash
uname -a                 # Kernel version & architecture
dmesg | less             # Kernel boot messages
cat /proc/cmdline        # Kernel boot parameters
ls /boot                 # Check kernel images
grub2-editenv list       # GRUB environment variables
```

---

### **Real-Time Production Issues**

1. **Server Boots into Wrong Target**
    
    * Symptom: GUI loads when only text mode needed, or server boots into rescue mode
        
    * Diagnosis: `systemctl get-default` → check `/etc/systemd/system/`[`default.target`](http://default.target)
        
    * Fix: Change default target:
        
        ```bash
        systemctl set-default multi-user.target
        ```
        
2. **Service Fails During Boot**
    
    * Symptom: Critical service fails, server partially boots
        
    * Diagnosis: `journalctl -b`, `systemctl --failed`
        
    * Fix: Check service logs, enable/restart service, fix dependencies
        
3. **Boot hangs / Slow boot**
    
    * Symptom: Server takes very long to boot
        
    * Diagnosis: `systemd-analyze blame` → shows slow services
        
    * Fix: Disable unnecessary services:
        
        ```bash
        systemctl disable <service>
        ```
        
4. **Kernel Upgrade / GRUB Issues**
    
    * Symptom: Server boots into old kernel or kernel panic
        
    * Diagnosis: `ls /boot`, `grub2-editenv list`, check `/etc/default/grub`
        
    * Fix: Update GRUB config:
        
        ```bash
        grub2-mkconfig -o /boot/grub2/grub.cfg
        ```
        
5. **Rescue / Emergency Mode**
    
    * Symptom: Server boots into rescue/emergency mode
        
    * Diagnosis: `journalctl -xb` → find failing units, check filesystem integrity
        
    * Fix: Repair filesystem or failed service, then reboot
        

---

## **Boot Process Deep Dive**

### **1️⃣ Linux Boot Sequence**

1. **BIOS / UEFI stage**
    
    * Power-on self-test (POST)
        
    * Loads bootloader from MBR (BIOS) or EFI partition (UEFI)
        
2. **Bootloader (GRUB2)**
    
    * Presents boot menu
        
    * Loads kernel and initramfs
        
    * Passes boot parameters to kernel (`/proc/cmdline`)
        
3. **Kernel Initialization**
    
    * Mounts root filesystem (from `initramfs`)
        
    * Initializes drivers, memory, storage, network
        
    * Starts `systemd` as PID 1
        
4. **Systemd Initialization**
    
    * Reads default target (`systemctl get-default`)
        
    * Activates units/services in dependency order
        
    * Switches to multi-user / graphical target
        
5. **User Space**
    
    * Services like SSH, web servers, databases start
        
    * Cron jobs, monitoring agents, firewall, SELinux enforcement applied
        

---

### **2️⃣ Single-User / Rescue Mode**

* **Purpose:** Minimal environment for recovery
    
* **Access:** `systemctl isolate` [`rescue.target`](http://rescue.target) or `systemctl isolate` [`emergency.target`](http://emergency.target)
    
* **Differences:**
    
    * Rescue: Starts root shell, minimal services, networking optional
        
    * Emergency: Only root shell, read-only root filesystem, used for severe recovery
        

**Commands to enter single-user mode:**

```bash
# Temporarily at boot
# Edit GRUB menu: add 'single' or 'systemd.unit=rescue.target' to kernel line

# From running system
systemctl isolate rescue.target
systemctl isolate emergency.target
```

**Common use cases:**

* Reset forgotten root password
    
* Repair broken filesystems
    
* Fix misconfigured system services
    

---

### **3️⃣ Common Boot Issues & Production Scenarios**

1. **Server Boots into Emergency Mode**
    
    * Cause: Filesystem errors, failed critical units, incorrect fstab
        
    * Check: `journalctl -xb`, `fsck /dev/sdX`
        
    * Fix: Repair filesystem, correct fstab entries, reboot
        
2. **GRUB Boot Menu Missing / Wrong Kernel**
    
    * Cause: GRUB config not updated after kernel upgrade
        
    * Check: `ls /boot`, `grub2-editenv list`, `cat /etc/default/grub`
        
    * Fix: Rebuild GRUB:
        
        ```bash
        grub2-mkconfig -o /boot/grub2/grub.cfg
        grub2-set-default <kernel_index>
        ```
        
3. **Kernel Panic**
    
    * Cause: Missing initramfs, incompatible kernel parameters, missing drivers
        
    * Fix: Boot into previous working kernel from GRUB, rebuild initramfs:
        
        ```bash
        dracut --force
        ```
        
4. **Root Filesystem Not Mounting**
    
    * Cause: Wrong UUID/LABEL in `/etc/fstab` or kernel parameters
        
    * Fix: Boot into rescue, correct `/etc/fstab` or `GRUB_CMDLINE_LINUX` parameters
        
5. **Slow Boot / Hanging Services**
    
    * Cause: Services failing to start, waiting for network or devices
        
    * Check: `systemd-analyze blame`, `systemctl list-units --failed`
        
    * Fix: Disable unnecessary services, fix failing units
        
6. **Forgot Root Password**
    
    * Boot into single-user or rescue mode
        
    * Mount root filesystem read-write if needed:
        
        ```bash
        mount -o remount,rw /
        passwd
        ```
        
7. **GRUB Password Protected**
    
    * Ensure you know the GRUB password or boot via rescue ISO
        
    * Remove password in `/boot/grub2/user.cfg` if necessary
        

---

### **4️⃣ Key GRUB Commands / Configurations**

```bash
# Show current GRUB environment
grub2-editenv list

# Generate new GRUB config (RHEL/CentOS)
grub2-mkconfig -o /boot/grub2/grub.cfg

# Set default boot entry
grub2-set-default 0

# Check boot entries
grep menuentry /boot/grub2/grub.cfg
```

**Important Files**

* `/boot/grub2/grub.cfg` → GRUB menu configuration
    
* `/etc/default/grub` → Default kernel & parameters
    
* `/boot/efi/EFI/redhat/grub.cfg` → UEFI GRUB config (RHEL/Fedora)
    

---

### **5️⃣ Real-Time Production Scenarios**

1. After a kernel update, server fails to boot → Use GRUB menu to boot previous kernel
    
2. `/var` full or `/tmp` corruption → Boot into rescue, clear space, reboot
    
3. Misconfigured fstab → Server drops to emergency mode, fix UUIDs
    
4. Filesystem corruption → Boot into single-user, run `fsck`
    
5. Boot hang due to service → Boot into rescue, disable failing systemd service
    

---

## **Boot Process & Troubleshooting — Interview Questions**

| Question | Answer / Approach |
| --- | --- |
| Explain the Linux boot sequence. | BIOS/UEFI → GRUB2 → Kernel/initramfs → systemd → user space |
| What is single-user mode? | Minimal environment for recovery; root access, no networking, used for maintenance |
| How to enter single-user or rescue mode? | Edit GRUB menu → add `single` or `systemd.unit=`[`rescue.target`](http://rescue.target); or `systemctl isolate` [`rescue.target`](http://rescue.target) |
| How to reset root password from single-user mode? | Remount root as read-write (`mount -o remount,rw /`) → `passwd` command |
| How to view and change the default boot target? | `systemctl get-default` / `systemctl set-default` [`multi-user.target`](http://multi-user.target) |
| How to list failed services during boot? | `systemctl --failed` / `journalctl -b` |
| How to troubleshoot boot into emergency mode? | Check `/etc/fstab`, run `fsck`, inspect failing units in `journalctl -xb` |
| How to fix GRUB boot menu after kernel update? | `grub2-mkconfig -o /boot/grub2/grub.cfg` / `grub2-set-default <entry>` |
| How to recover from kernel panic? | Boot previous kernel, rebuild initramfs (`dracut --force`), check boot params |
| How to debug slow boot or hanging services? | `systemd-analyze blame` → check slow or failed services → disable or fix units |

**Production-specific questions**

| Question | Answer / Approach |
| --- | --- |
| Server drops into emergency mode after reboot, what to do? | Boot into rescue → check `/etc/fstab`, repair filesystem, check failed units |
| How to boot an old kernel if new one fails? | Use GRUB menu → select previous kernel → `grub2-set-default` to make permanent |
| What if `/var` or `/tmp` full prevents boot? | Boot into rescue → free space, clean logs or YUM cache → reboot |
| GRUB password prevents boot, what to do? | Boot via rescue ISO, edit `/boot/grub2/user.cfg` or remove password temporarily |

---

## **Hands-On Tasks**

1. **Check current boot target**
    

```bash
systemctl get-default
systemctl list-units --type=target
```

2. **Temporarily switch target**
    

```bash
systemctl isolate rescue.target
systemctl isolate multi-user.target
```

3. **Inspect boot logs**
    

```bash
journalctl -b
systemctl --failed
systemd-analyze blame
```

4. **Reset root password in single-user mode**
    

```bash
# Boot into single-user mode from GRUB
mount -o remount,rw /
passwd
```

5. **Repair corrupted filesystem**
    

```bash
# Boot into rescue/emergency
fsck -y /dev/sdX
```

6. **Check and rebuild GRUB config**
    

```bash
grep menuentry /boot/grub2/grub.cfg
grub2-set-default 0
grub2-mkconfig -o /boot/grub2/grub.cfg
grub2-editenv list
```

7. **Boot previous kernel**
    

```bash
# At GRUB menu, select older kernel
# Make it default if stable
grub2-set-default <kernel_index>
```

8. **Simulate /var full affecting boot**
    

```bash
# Fill /var (for lab/testing only)
dd if=/dev/zero of=/var/fillfile bs=1M count=1024
# Boot rescue, remove large file
rm -f /var/fillfile
```

9. **Inspect kernel boot parameters**
    

```bash
cat /proc/cmdline
```

---

✅ **Outcome:**  
After this topic, you will be able to:

* Explain and troubleshoot Linux boot sequence
    
* Work with single-user, rescue, and emergency modes for recovery
    
* Manage and fix GRUB bootloader issues and kernel boot problems
    
* Handle real-world production issues like boot hangs, disk full, failed units
    
* Confidently answer interviews on boot process and recovery scenarios
    

---

## **1️⃣ Commands**

### **View Permissions**

```bash
ls -l /path/to/file
stat /path/to/file
```

### **Change Permissions**

```bash
chmod 755 file            # rwx for owner, rx for group/others
chmod u+x file            # Add execute to owner
chmod g-w file            # Remove write from group
chmod o=r file            # Others: read only
chmod 4755 file           # Setuid example
chmod 2755 dir            # Setgid example on directory
chmod +t dir              # Sticky bit
```

### **Change Ownership**

```bash
chown user:group file
chown -R user:group /path/to/dir
```

### **Access Control Lists (ACLs)**

```bash
# View ACL
getfacl file

# Set ACL
setfacl -m u:username:rwx file

# Remove ACL
setfacl -x u:username file
```

---

## **2️⃣ Real-Time Production Issues**

1. **Permissions Prevent Service Start**
    
    * Example: Web server cannot read `/var/www/html`
        
    * Fix: `chown -R apache:apache /var/www/html` and set proper `chmod`
        
2. **Shared Directory Issues**
    
    * Example: Team cannot write to shared dir
        
    * Fix: Set **setgid** on directory so new files inherit group:
        
        ```bash
        chmod g+s /shared/dir
        ```
        
3. **Sticky Bit for /tmp**
    
    * Example: Users deleting other users’ files
        
    * Fix: `chmod +t /tmp`
        
4. **Unauthorized Access / Security Breach**
    
    * Example: Sensitive files readable by everyone
        
    * Fix: Correct `chmod 600` or ACLs, restrict access
        
5. **NFS / CIFS Mounted Filesystem Issues**
    
    * Example: Files appear owned by `nobody`
        
    * Fix: Check mount options (`uid`, `gid`, `maproot`, `mapall`)
        

---

## **File Permission Management — Interview Questions**

| Question | Answer / Approach |
| --- | --- |
| How do you check file permissions? | `ls -l filename` or `stat filename` |
| What do the permission symbols mean (`rwxr-xr--`)? | Owner: `rwx`, Group: `r-x`, Others: `r--` |
| How do you change file permissions? | `chmod 755 file`, or symbolic: `chmod u+x,g-w file` |
| How do you change ownership of a file or directory? | `chown user:group file` or recursively: `chown -R user:group /dir` |
| What is `setuid`, `setgid`, and sticky bit? | setuid: execute as owner, setgid: inherit group, sticky: only owner can delete files |
| How do you view ACLs on a file? | `getfacl file` |
| How do you set ACLs for specific users? | `setfacl -m u:username:rwx file` |
| How do you remove ACL entries? | `setfacl -x u:username file` |
| How to troubleshoot permission issues for services? | Check ownership (`ls -l`), permissions (`chmod`), ACLs (`getfacl`), SELinux contexts (`ls -Z`) |
| How to ensure shared directories allow group collaboration? | Set group ownership + setgid on directory: `chown :group /shared`, `chmod g+s /shared` |

**Production-specific questions**

| Question | Answer / Approach |
| --- | --- |
| Web server cannot access `/var/www/html` | Check ownership: `chown -R apache:apache /var/www/html`; set correct permissions `chmod 755 /var/www/html` |
| Users deleting each other’s files in `/tmp` | Ensure sticky bit: `chmod +t /tmp` |
| Sensitive files readable by everyone | Correct permissions: `chmod 600 /path/to/file` |
| NFS-mounted directory shows wrong owner | Check mount options, use `uid`, `gid`, `maproot`, `mapall` |

---

## **Hands-On Tasks**

1. **Check file permissions**
    

```bash
ls -l /etc/passwd
stat /etc/passwd
```

2. **Change file permissions**
    

```bash
chmod 644 /tmp/testfile        # Owner read/write, others read-only
chmod u+x /tmp/testfile        # Add execute for owner
chmod g-w /tmp/testfile        # Remove write for group
```

3. **Change ownership**
    

```bash
chown user1:user1 /tmp/testfile
chown -R user1:developers /shared
```

4. **Set special permissions**
    

```bash
chmod 4755 /usr/bin/script       # setuid example
chmod 2755 /shared               # setgid example for directory
chmod +t /tmp                    # sticky bit for shared temp dir
```

5. **View and modify ACLs**
    

```bash
getfacl /tmp/testfile
setfacl -m u:devops:rwx /tmp/testfile
setfacl -x u:devops /tmp/testfile
```

6. **Simulate production scenario**
    

```bash
# Web server cannot read files
mkdir /var/www/html/test
touch /var/www/html/test/index.html
chown root:root /var/www/html/test
chmod 700 /var/www/html/test
# Fix permissions
chown -R apache:apache /var/www/html/test
chmod 755 /var/www/html/test
```

---

✅ **Outcome:**  
After this topic, you will be able to:

* Understand Linux file permissions, ownership, and ACLs
    
* Troubleshoot permission issues for services and shared directories
    
* Configure special permissions (setuid/setgid/sticky) correctly
    
* Handle real-world production permission issues
    
* Answer interview questions confidently on file access and security
    

---

## **1️⃣ Common Login Issues & Production Scenarios**

### **A. Incorrect Password / Authentication Failure**

* **Symptom:** User enters correct credentials, login fails.
    
* **Check:**
    
    1. `/var/log/secure` (RHEL/CentOS) or `/var/log/auth.log` (Debian/Ubuntu)
        
        ```bash
        grep username /var/log/secure
        ```
        
    2. PAM failures (`pam_unix`, `pam_sss`, etc.)
        
    3. Locked account:
        
        ```bash
        passwd -S username
        ```
        
    4. Expired password:
        
        ```bash
        chage -l username
        ```
        
* **Fix:** Unlock account, reset password, check PAM or SSSD configuration.
    

---

### **B. Account Locked or Expired**

* **Symptom:** User account disabled or expired.
    
* **Check:**
    
    ```bash
    chage -l username
    passwd -S username
    ```
    
* **Fix:**
    
    ```bash
    passwd -u username      # unlock account
    chage -E 2026-12-31 username  # extend expiration
    ```
    

---

### **C. Home Directory / Shell Issues**

* **Symptom:** User logs in but cannot access shell or home directory.
    
* **Check:**
    
    ```bash
    grep username /etc/passwd
    ls -ld /home/username
    ```
    
* **Fix:**
    
    ```bash
    usermod -s /bin/bash username      # set shell
    mkdir -p /home/username
    chown username:username /home/username
    chmod 700 /home/username
    ```
    

---

### **D. Permissions / Sudo Issues**

* **Symptom:** User cannot execute commands via sudo.
    
* **Check:**
    
    ```bash
    sudo -l -U username
    ```
    
* **Fix:** Add user to sudoers or correct `/etc/sudoers` or group membership:
    
    ```bash
    usermod -aG wheel username        # RHEL/CentOS
    ```
    

---

### **E. LDAP / FreeIPA / SSSD Authentication Failures**

* **Symptom:** Domain users cannot login; works locally.
    
* **Check:**
    
    ```bash
    systemctl status sssd
    getent passwd username
    tail -f /var/log/sssd/sssd.log
    ```
    
* **Fix:**
    
    * Restart SSSD:
        
        ```bash
        systemctl restart sssd
        ```
        
    * Clear cache:
        
        ```bash
        sss_cache -E
        ```
        
    * Check Kerberos tickets:
        
        ```bash
        klist
        ```
        

---

### **F. SSH Login Failures**

* **Symptom:** User cannot login remotely via SSH.
    
* **Check:**
    
    1. SSH logs:
        
        ```bash
        tail -f /var/log/secure | grep sshd
        ```
        
    2. `/etc/ssh/sshd_config` restrictions (PermitRootLogin, AllowUsers, AllowGroups)
        
* **Fix:** Correct SSH config, restart SSHD:
    
    ```bash
    systemctl restart sshd
    ```
    

---

### **G. PAM / Authentication Module Failures**

* **Symptom:** All users failing login.
    
* **Check:** `/var/log/secure`, `journalctl -xe`
    
* **Fix:** Ensure PAM configuration files in `/etc/pam.d/` are correct.
    

---

### **H. Disk Full / Home Directory Issues**

* **Symptom:** User cannot login; errors like “Cannot write to home directory”.
    
* **Check:**
    
    ```bash
    df -h
    quota -u username
    ```
    
* **Fix:** Free space, clean logs, increase quota.
    

---

### **I. Locked out due to failed attempts / fail2ban**

* **Symptom:** User temporarily blocked after multiple failed login attempts.
    
* **Check:**
    
    ```bash
    faillog -u username
    ```
    
* **Fix:** Reset failed attempts:
    
    ```bash
    faillog -r -u username
    ```
    

---

### **2️⃣ Checklist as Linux Admin for Login Issues**

1. **Authentication logs** → `/var/log/secure`, `/var/log/auth.log`
    
2. **Account status** → `passwd -S`, `chage -l`
    
3. **Home directory & shell** → permissions, existence, shell correctness
    
4. **Group membership** → required for sudo or service access
    
5. **SSSD / LDAP / FreeIPA / Kerberos** → domain connectivity, caches, tickets
    
6. **Disk / quota** → ensure enough space for login and shell initialization
    
7. **SSH / PAM / security modules** → configuration, restrictions, fail2ban
    
8. **SELinux** → sometimes prevents login (check `audit.log` and `ausearch`)
    

---

## **1️⃣ Disk & Filesystem Issues**

**Common Problems:**

* Disk full `/var`, `/home`, `/tmp`
    
* Filesystem corruption
    
* Mount failures
    
* Slow I/O
    

**Commands to troubleshoot:**

```bash
df -h           # disk usage
du -sh /path    # folder size
lsblk           # disk info
mount           # check mounted filesystems
xfs_info /dev/sdX  # xfs details
dmesg | grep sd  # disk errors
```

**Real-Time Fixes:**

* Clean logs: `rm -f /var/log/*.old`
    
* Extend logical volumes: `lvextend`, `xfs_growfs`
    
* Repair filesystems: `fsck` or `xfs_repair`
    
* Check for blocked processes causing I/O: `iotop`, `lsof`
    

---

## **2️⃣ Memory & CPU Issues**

**Common Problems:**

* High CPU usage by processes
    
* Memory leaks causing OOM
    
* Swap exhaustion
    

**Commands:**

```bash
top / htop
vmstat 1
free -m
ps aux --sort=-%mem | head
ps aux --sort=-%cpu | head
```

**Real-Time Fixes:**

* Kill runaway process: `kill -9 PID`
    
* Check logs for memory leaks
    
* Tune services (e.g., reduce worker threads)
    
* Increase swap if needed: `fallocate -l 2G /swapfile && mkswap /swapfile && swapon /swapfile`
    

---

## **3️⃣ Network Issues**

**Common Problems:**

* Network interface down
    
* DNS resolution failures
    
* Packet loss or high latency
    
* Service not reachable
    

**Commands:**

```bash
ip a / ip -br addr
ss -tulnp
ping / traceroute
dig google.com
nmcli dev status
```

**Real-Time Fixes:**

* Restart network service: `systemctl restart NetworkManager`
    
* Check firewall rules: `firewall-cmd --list-all`
    
* Verify DNS: `/etc/resolv.conf`
    
* Check interface: `ip link set dev eth0 up`
    

---

## **4️⃣ Service / Process Issues**

**Common Problems:**

* Service not starting
    
* Crashed daemon
    
* Failed dependencies
    

**Commands:**

```bash
systemctl status <service>
systemctl restart <service>
journalctl -u <service> -b
ps aux | grep <service>
```

**Real-Time Fixes:**

* Check logs: `/var/log/<service>.log` or `journalctl`
    
* Fix configuration errors
    
* Enable service at boot: `systemctl enable <service>`
    

---

## **5️⃣ Package / Yum / DNF Issues**

**Common Problems:**

* Repository not reachable
    
* Failed package installation
    
* `/var` full while installing packages
    

**Commands:**

```bash
yum repolist
yum list installed
yum clean all
rpm -qa | grep <package>
```

**Real-Time Fixes:**

* Fix repo config: `/etc/yum.repos.d/`
    
* Clean cache: `yum clean all`
    
* Free `/var` space
    

---

## **6️⃣ Log & Monitoring Issues**

**Common Problems:**

* Logs filling disk
    
* Rsyslog/journalctl misconfiguration
    
* Unable to rotate logs
    

**Commands:**

```bash
journalctl -b
tail -f /var/log/messages
ls -lh /var/log
logrotate -d /etc/logrotate.conf
```

**Real-Time Fixes:**

* Rotate logs: `logrotate -f /etc/logrotate.conf`
    
* Delete old logs
    
* Increase log retention or move logs to centralized system
    

---

## **7️⃣ SELinux / Permission Issues**

**Common Problems:**

* Services fail due to SELinux
    
* Files not accessible due to permissions
    
* Shared directories misconfigured
    

**Commands:**

```bash
getenforce
sestatus
ls -lZ /path/to/file
chcon -t httpd_sys_content_t /var/www/html/index.html
```

**Real-Time Fixes:**

* Temporarily disable SELinux: `setenforce 0`
    
* Correct SELinux contexts
    
* Fix permissions or ACLs
    

---

## **8️⃣ Boot / Kernel / GRUB Issues**

**Common Problems:**

* Server hangs at boot
    
* Emergency/rescue mode
    
* Kernel panic
    
* GRUB misconfiguration
    

**Commands:**

```bash
systemctl get-default
journalctl -b
dmesg | less
grub2-editenv list
ls /boot
```

**Real-Time Fixes:**

* Boot into rescue/single-user mode
    
* Rebuild GRUB: `grub2-mkconfig -o /boot/grub2/grub.cfg`
    
* Repair filesystem: `fsck`
    
* Switch to previous working kernel
    

---

## **9️⃣ Cron & Scheduled Job Issues**

**Common Problems:**

* Jobs not running
    
* Output not logged
    
* Multiple instances
    

**Commands:**

```bash
crontab -l
systemctl status crond
tail -f /var/log/cron
```

**Real-Time Fixes:**

* Check script permissions
    
* Redirect stdout/stderr
    
* Prevent overlapping runs using `flock`
    
* Restart cron service
    

---

✅ **Key Takeaways for Interviews:**

* Be ready to explain **troubleshooting steps**, not just commands.
    
* Always mention **checking logs** first (`journalctl`, `/var/log/*`) for diagnosis.
    
* Know **how to fix common production issues** quickly and safely.
    

---

# **Linux Admin Real-Time Issues — Interview Prep Sheet**

---

## **1️⃣ Disk & Filesystem Issues**

**Common Problems:**

* Disk full (`/var`, `/home`, `/tmp`)
    
* Filesystem corruption
    
* Mount failures
    
* Slow I/O
    

**Commands:**

```bash
df -h                 # Disk usage
du -sh /path           # Folder size
lsblk                  # Disk info
mount                  # Check mounted filesystems
xfs_info /dev/sdX      # XFS details
dmesg | grep sd        # Disk errors
```

**Fixes / Real-Time Solutions:**

* Clean logs: `rm -f /var/log/*.old`
    
* Extend LVM: `lvextend -L +10G /dev/mapper/vg-lv` + `xfs_growfs /dev/mapper/vg-lv`
    
* Repair filesystem: `fsck /dev/sdX` or `xfs_repair /dev/sdX`
    
* Check blocked processes causing I/O: `iotop`, `lsof`
    

---

## **2️⃣ Memory & CPU Issues**

**Common Problems:**

* High CPU usage
    
* Memory leaks / OOM
    
* Swap exhaustion
    

**Commands:**

```bash
top / htop
vmstat 1
free -m
ps aux --sort=-%mem | head
ps aux --sort=-%cpu | head
```

**Fixes:**

* Kill runaway process: `kill -9 PID`
    
* Check logs for memory leaks
    
* Tune service configuration (threads, caching)
    
* Add swap if needed:
    

```bash
fallocate -l 2G /swapfile
mkswap /swapfile
swapon /swapfile
```

---

## **3️⃣ Network Issues**

**Common Problems:**

* Interface down
    
* DNS failure
    
* Packet loss / high latency
    
* Services unreachable
    

**Commands:**

```bash
ip a / ip -br addr
ss -tulnp
ping / traceroute
dig google.com
nmcli dev status
```

**Fixes:**

* Restart network: `systemctl restart NetworkManager`
    
* Check firewall: `firewall-cmd --list-all`
    
* Verify DNS: `cat /etc/resolv.conf`
    
* Bring interface up: `ip link set dev eth0 up`
    

---

## **4️⃣ Service / Process Issues**

**Common Problems:**

* Service not starting
    
* Crashed daemons
    
* Dependency failures
    

**Commands:**

```bash
systemctl status <service>
systemctl restart <service>
journalctl -u <service> -b
ps aux | grep <service>
```

**Fixes:**

* Inspect logs: `/var/log/<service>.log`, `journalctl`
    
* Fix configuration errors
    
* Enable service at boot: `systemctl enable <service>`
    

---

## **5️⃣ Package Management Issues (Yum / DNF)**

**Common Problems:**

* Repo unreachable
    
* Failed package install
    
* `/var` full
    

**Commands:**

```bash
yum repolist
yum list installed
yum clean all
rpm -qa | grep <package>
```

**Fixes:**

* Fix repo config: `/etc/yum.repos.d/*.repo`
    
* Clean cache: `yum clean all`
    
* Free `/var` space
    

---

## **6️⃣ Log & Monitoring Issues**

**Common Problems:**

* Logs filling disk
    
* Misconfigured rsyslog/journalctl
    
* Failed log rotation
    

**Commands:**

```bash
journalctl -b
tail -f /var/log/messages
ls -lh /var/log
logrotate -d /etc/logrotate.conf
```

**Fixes:**

* Force logrotate: `logrotate -f /etc/logrotate.conf`
    
* Delete old logs
    
* Move logs to centralized logging
    

---

## **7️⃣ SELinux / Permission Issues**

**Common Problems:**

* Services blocked by SELinux
    
* File access denied
    
* Shared directories misconfigured
    

**Commands:**

```bash
getenforce
sestatus
ls -lZ /path/to/file
chcon -t httpd_sys_content_t /var/www/html/index.html
```

**Fixes:**

* Temporarily disable SELinux: `setenforce 0`
    
* Correct SELinux context: `restorecon -Rv /path`
    
* Fix file permissions / ACLs
    

---

## **8️⃣ Boot / Kernel / GRUB Issues**

**Common Problems:**

* Boot hangs / rescue mode
    
* Kernel panic
    
* GRUB misconfiguration
    

**Commands:**

```bash
systemctl get-default
journalctl -b
dmesg | less
grub2-editenv list
ls /boot
```

**Fixes:**

* Boot into rescue/single-user
    
* Rebuild GRUB: `grub2-mkconfig -o /boot/grub2/grub.cfg`
    
* Repair filesystem: `fsck`
    
* Switch kernel: select previous working kernel in GRUB
    

---

## **9️⃣ Cron / Scheduled Jobs Issues**

**Common Problems:**

* Jobs not executing
    
* Multiple instances
    
* Output not logged
    

**Commands:**

```bash
crontab -l
systemctl status crond
tail -f /var/log/cron
```

**Fixes:**

* Check script permissions
    
* Redirect stdout/stderr: `>> /var/log/script.log 2>&1`
    
* Prevent overlapping runs: `flock /tmp/lockfile`
    
* Restart cron: `systemctl restart crond`
    

---

## **10️⃣ File Permission / ACL Issues**

**Common Problems:**

* Service fails to access files
    
* Shared directories misconfigured
    
* Unauthorized file access
    

**Commands:**

```bash
ls -l /path
chmod 755 file
chown user:group file
getfacl file
setfacl -m u:username:rwx file
```

**Fixes:**

* Correct permissions: `chmod / chown`
    
* Set ACLs for specific users/groups
    
* Use sticky bit for shared dirs: `chmod +t /tmp`
    
* Use setgid for collaborative directories: `chmod g+s /shared`
    

---

✅ **Tips for Interview Prep:**

* Always **explain the troubleshooting flow**, not just commands.
    
* Mention **logs first** (`journalctl`, `/var/log/*`) for diagnosing issues.
    
* Relate your answers to **real production scenarios**, e.g., `/var full`, high CPU, service down, boot failure.
    
* Know both **commands and their reasoning** for solving problems.
    

---

# **Linux Admin Hands-On Real-Time Practice Sheet**

---

## **1️⃣ Disk & Filesystem**

**Scenario 1:** `/var` is full; services failing  
**Tasks:**

```bash
df -h
du -sh /var/*
ls -lh /var/log
rm -f /var/log/*.old
yum clean all
```

**Extra:** Extend LVM partition:

```bash
lvextend -L +5G /dev/mapper/vg-lv
xfs_growfs /dev/mapper/vg-lv
```

**Scenario 2:** Filesystem corruption

```bash
umount /dev/sdb1
fsck -y /dev/sdb1
mount /dev/sdb1 /mnt
```

---

## **2️⃣ Memory & CPU**

**Scenario:** High memory / CPU usage by a process  
**Tasks:**

```bash
top
ps aux --sort=-%mem | head
ps aux --sort=-%cpu | head
kill -9 <PID>
vmstat 1
free -m
```

**Extra:** Create swap file for low-memory VM:

```bash
fallocate -l 2G /swapfile
mkswap /swapfile
swapon /swapfile
```

---

## **3️⃣ Network**

**Scenario:** Server cannot reach external services  
**Tasks:**

```bash
ip -br addr
ping 8.8.8.8
dig google.com
ss -tulnp
nmcli dev status
systemctl restart NetworkManager
```

**Extra:** Check firewall rules:

```bash
firewall-cmd --list-all
firewall-cmd --reload
```

---

## **4️⃣ Service / Process**

**Scenario:** Apache / Nginx not starting  
**Tasks:**

```bash
systemctl status httpd
journalctl -u httpd -b
systemctl restart httpd
```

**Extra:** Enable at boot:

```bash
systemctl enable httpd
```

---

## **5️⃣ Package Management**

**Scenario:** Cannot install package  
**Tasks:**

```bash
yum repolist
yum list installed | grep <package>
yum install <package>
yum clean all
```

**Extra:** Check disk space if `/var` full

---

## **6️⃣ Logs & Monitoring**

**Scenario:** Logs filling disk rapidly  
**Tasks:**

```bash
ls -lh /var/log
tail -f /var/log/messages
journalctl -b
logrotate -d /etc/logrotate.conf
logrotate -f /etc/logrotate.conf
```

**Extra:** Move old logs to archive directory

---

## **7️⃣ SELinux & Permissions**

**Scenario:** Web service cannot read files  
**Tasks:**

```bash
getenforce
sestatus
ls -lZ /var/www/html/index.html
chcon -t httpd_sys_content_t /var/www/html/index.html
```

**Extra:** Fix file permissions:

```bash
chmod 755 /var/www/html
chown -R apache:apache /var/www/html
```

---

## **8️⃣ Boot / GRUB / Kernel**

**Scenario:** Server hangs after reboot  
**Tasks:**

* Boot into rescue mode via GRUB → single-user mode
    

```bash
systemctl get-default
journalctl -b
dmesg | less
```

* Repair filesystem:
    

```bash
fsck /dev/sda1
```

* Rebuild GRUB config:
    

```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
```

---

## **9️⃣ Cron Jobs**

**Scenario:** Cron job not executing  
**Tasks:**

```bash
crontab -l
systemctl status crond
tail -f /var/log/cron
# Ensure script is executable
chmod +x /path/to/script.sh
```

**Extra:** Prevent overlapping jobs:

```bash
flock /tmp/myjob.lock /path/to/script.sh
```

---

## **🔟 File Permissions / ACL**

**Scenario:** Service cannot access shared folder  
**Tasks:**

```bash
ls -l /shared
chmod 775 /shared
chown :developers /shared
chmod g+s /shared  # setgid for new files
setfacl -m u:devops:rwx /shared
```

**Extra:** Sticky bit on temp dirs:

```bash
chmod +t /tmp
```

---

### **Tips for Practice:**

* Always **simulate production issues in a lab VM**.
    
* Combine issues: e.g., `/var` full + service down.
    
* Document commands and outputs — this will help for **interviews and troubleshooting**.
    
* Use `journalctl` and `/var/log` as your first diagnostic step in every scenario.
    

---

# **1–2 Days Rapid Linux Admin Lab Plan**

**Goal:** Cover all critical Linux admin topics and real-world scenarios with hands-on practice.  
**Duration:** ~8–10 hours per day, split into topics.

---

## **Day 1: Core Linux & System Monitoring**

### **Session 1: System Info & Monitoring (1 hr)**

* Commands:
    

```bash
uname -a
uptime
top / htop
free -m
vmstat 1
iostat -xz 1
sar -u 1 3
```

* Hands-On:
    
    * Observe CPU, memory, I/O usage
        
    * Simulate load: `stress --cpu 2 --vm 1 --timeout 60s`
        
* Real-Time Issue:
    
    * Identify high CPU/memory processes
        
* Interview:
    
    * Difference between `top` and `htop`
        
    * What is load average?
        

---

### **Session 2: Process Management (1 hr)**

* Commands:
    

```bash
ps aux | grep <process>
kill -9 <PID>
nice -n 10 <command>
renice -n -5 -p <PID>
systemctl status <service>
```

* Hands-On:
    
    * Kill runaway process
        
    * Change priority of a CPU-heavy process
        
* Real-Time Issue:
    
    * Service not responding → check logs, restart
        

---

### **Session 3: Disk & Filesystem (1.5 hr)**

* Commands:
    

```bash
df -h
du -sh /path
lsblk
mount / unmount
xfs_info /dev/sdX
```

* Hands-On:
    
    * Fill `/var` to simulate full disk → clean logs
        
    * Extend LVM, repair XFS with `xfs_repair`
        
* Real-Time Issue:
    
    * Services failing because `/var` is full
        

---

### **Session 4: Networking (1 hr)**

* Commands:
    

```bash
ip -br addr
ss -tulnp
ping / traceroute
dig google.com
nmcli dev status
```

* Hands-On:
    
    * Bring interface up/down
        
    * Fix DNS or routing issues
        
* Real-Time Issue:
    
    * Service unreachable, check firewall, DNS
        

---

### **Session 5: User & Permission Management (1 hr)**

* Commands:
    

```bash
useradd / usermod / passwd
groups username
ls -l /path
chmod / chown
getfacl / setfacl
```

* Hands-On:
    
    * Create user, assign groups
        
    * Fix permission issue for web service
        
    * Apply ACLs for collaborative folder
        
* Real-Time Issue:
    
    * Service cannot read files due to wrong permissions
        

---

## **Day 2: Advanced Admin & Troubleshooting**

### **Session 6: Package & Repo Management (1 hr)**

* Commands:
    

```bash
yum check-update
yum list installed
yum repolist
yum install <package>
yum clean all
```

* Hands-On:
    
    * Install package after cleaning cache
        
    * Simulate repo unreachable → fix `.repo` file
        
* Real-Time Issue:
    
    * `/var` full during package installation
        

---

### **Session 7: Logs & Monitoring (1 hr)**

* Commands:
    

```bash
journalctl -b
tail -f /var/log/messages
logrotate -d /etc/logrotate.conf
```

* Hands-On:
    
    * Rotate logs manually
        
    * Simulate logs filling `/var`
        
* Real-Time Issue:
    
    * Services failing due to full `/var/log`
        

---

### **Session 8: SELinux & Firewall (1 hr)**

* Commands:
    

```bash
getenforce
sestatus
ls -lZ /path
chcon -t httpd_sys_content_t /var/www/html
firewall-cmd --list-all
```

* Hands-On:
    
    * Fix SELinux blocking HTTP service
        
    * Allow port 8080 in firewall
        
* Real-Time Issue:
    
    * Services blocked due to SELinux or firewall
        

---

### **Session 9: Boot / GRUB / Kernel (1 hr)**

* Commands:
    

```bash
systemctl get-default
journalctl -b
dmesg | less
grub2-mkconfig -o /boot/grub2/grub.cfg
fsck /dev/sda1
```

* Hands-On:
    
    * Boot into single-user mode
        
    * Repair filesystem
        
    * Switch to previous kernel if new kernel fails
        
* Real-Time Issue:
    
    * Emergency/rescue mode after reboot
        

---

### **Session 10: Cron & Scheduled Jobs (0.5 hr)**

* Commands:
    

```bash
crontab -l
systemctl status crond
tail -f /var/log/cron
```

* Hands-On:
    
    * Create a cron job
        
    * Simulate job failing → fix permissions/output logging
        
* Real-Time Issue:
    
    * Job not running due to wrong permissions or overlapping runs
        

---

### **Session 11: Hands-On Combined Scenario Lab (2 hr)**

* Simulate a real production server:
    
    1. `/var` 90% full
        
    2. Apache failing due to permissions
        
    3. SSH login issues for a domain user
        
    4. Cron job failing
        
* Solve all problems step-by-step using the commands learned
    
* Document **logs, fixes, and commands**
    

---

### **Tips for Rapid Practice**

1. Use **virtual machines** (VMware / VirtualBox / KVM) for each session.
    
2. Focus on **logs first**, then **commands**, then **fixes**.
    
3. Keep **cheat sheet** for commands handy.
    
4. After completing Day 1 & 2, try **mixing multiple issues** in one VM to simulate real production troubleshooting.
    

---

# **Linux Admin Real-Time Issues Cheat Sheet**

---

## **1️⃣ System Info & Monitoring**

```bash
uname -a                 # Kernel & OS info
uptime                   # System uptime & load
top / htop               # CPU, memory, process usage
free -m                  # Memory usage
vmstat 1                 # CPU, memory, I/O stats
iostat -xz 1             # Disk I/O stats
sar -u 1 3               # CPU usage history
```

**Real-Time Fixes:**

* Identify high CPU/memory processes
    
* Kill runaway processes: `kill -9 <PID>`
    
* Monitor system under load using `stress`
    

---

## **2️⃣ Process Management**

```bash
ps aux | grep <process>
kill -9 <PID>
nice -n 10 <command>
renice -n -5 -p <PID>
systemctl status <service>
systemctl restart <service>
```

**Real-Time Fixes:**

* Service not responding → check logs, restart
    
* Change priority for heavy processes
    

---

## **3️⃣ Disk & Filesystem**

```bash
df -h                   # Disk usage
du -sh /path             # Folder size
lsblk                    # Disk info
mount / umount           # Mount/unmount filesystems
xfs_info /dev/sdX        # XFS details
fsck /dev/sdX            # Repair filesystem
lvextend -L +5G /dev/mapper/vg-lv
xfs_growfs /dev/mapper/vg-lv
```

**Real-Time Fixes:**

* Free `/var` space (logs, cache)
    
* Repair corrupted filesystem
    
* Extend LVM volume
    

---

## **4️⃣ Networking**

```bash
ip -br addr
ss -tulnp
ping 8.8.8.8
traceroute google.com
dig google.com
nmcli dev status
systemctl restart NetworkManager
firewall-cmd --list-all
firewall-cmd --reload
```

**Real-Time Fixes:**

* Interface down → `ip link set dev eth0 up`
    
* DNS / routing issues → check `/etc/resolv.conf`, routes
    
* Firewall blocking ports
    

---

## **5️⃣ User & Permission Management**

```bash
useradd / usermod / passwd
groups username
ls -l /path
chmod 755 file
chown user:group file
getfacl file
setfacl -m u:username:rwx file
chmod +t /tmp            # Sticky bit
chmod g+s /shared        # setgid for directories
```

**Real-Time Fixes:**

* Fix permissions for services
    
* Resolve ACL issues for shared directories
    
* Unlock or reset user accounts
    

---

## **6️⃣ Package Management (Yum / DNF)**

```bash
yum check-update
yum list installed
yum repolist
yum install <package>
yum clean all
rpm -qa | grep <package>
```

**Real-Time Fixes:**

* Repo unreachable → fix `.repo` file
    
* `/var` full → clean cache
    
* Failed package install → check dependencies
    

---

## **7️⃣ Logs & Monitoring**

```bash
journalctl -b
tail -f /var/log/messages
logrotate -d /etc/logrotate.conf
logrotate -f /etc/logrotate.conf
```

**Real-Time Fixes:**

* Rotate logs manually
    
* Delete old logs
    
* Move logs to centralized storage
    

---

## **8️⃣ SELinux & Firewall**

```bash
getenforce
sestatus
ls -lZ /path/to/file
chcon -t httpd_sys_content_t /var/www/html
```

**Real-Time Fixes:**

* Disable SELinux temporarily: `setenforce 0`
    
* Correct SELinux contexts: `restorecon -Rv /path`
    
* Fix firewall issues blocking services
    

---

## **9️⃣ Boot / GRUB / Kernel**

```bash
systemctl get-default
journalctl -b
dmesg | less
grub2-mkconfig -o /boot/grub2/grub.cfg
fsck /dev/sda1
```

**Real-Time Fixes:**

* Boot into single-user / rescue mode
    
* Repair filesystem
    
* Rebuild GRUB configuration
    
* Switch kernel if new kernel fails
    

---

## **🔟 Cron & Scheduled Jobs**

```bash
crontab -l
systemctl status crond
tail -f /var/log/cron
chmod +x /path/to/script.sh
flock /tmp/myjob.lock /path/to/script.sh
```

**Real-Time Fixes:**

* Cron job not running → check permissions
    
* Redirect stdout/stderr for logging
    
* Prevent overlapping jobs using `flock`
    

---

## **✅ Quick Real-Time Production Scenarios**

| Issue | Command / Check | Fix |
| --- | --- | --- |
| `/var` full | `df -h`, `du -sh /var/*` | Delete old logs, clean cache, extend LV |
| Service fails | `systemctl status`, `journalctl` | Check logs, restart, fix config |
| Disk error | \`dmesg | grep sd\` |
| High CPU/memory | `top`, `ps aux --sort=-%cpu/%mem` | Kill or renice process |
| User cannot access files | `ls -l`, `getfacl` | Fix permissions, ACL, SELinux |
| Network down | `ip addr`, `ping`, `dig` | Restart NetworkManager, check firewall |
| Package install fails | `yum repolist`, `yum clean all` | Fix repo, free space, resolve dependencies |
| Logs filling disk | `journalctl`, `tail -f` | Rotate logs, archive old logs |
| Boot failure | `journalctl -b`, `dmesg` | Rescue mode, repair FS, rebuild GRUB |

---