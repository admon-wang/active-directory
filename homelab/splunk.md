# Splunk Virtual Machine Setup Instructions & Lab Notes

## Objective
The objective of this phase is to prepare the Ubuntu Server virtual machine that will host **Splunk Enterprise** for our Active Directory monitoring lab. This involves configuring static networking, verifying external internet access, handling package manager locks, and mounting a shared host folder to access the Splunk installation package.

---

## 1. Network Configuration (Netplan)

### Initial State: Wrong IP Address
<img width="982" height="323" alt="project2" src="https://github.com/user-attachments/assets/337784b4-deef-42fc-b419-c146eb8e8405" />

#### Why was it wrong?
- By default, Ubuntu Server receives a dynamic IP address via DHCP assigned by the hypervisor's default virtual network (often on an arbitrary subnet like `192.168.something` or `10.0.2.x`).
- For our Active Directory lab topology, the Splunk SIEM needs a **predictable, static IP address** (`192.168.10.10/24`) so our Domain Controller, targets, and Universal Forwarders know exactly where to ship logs without worrying about DHCP lease expirations changing the address.

---

### How to Configure it Correctly

We modify our Netplan configuration file (`/etc/netplan/00-installer-config.yaml` or similar):

<img width="477" height="317" alt="project1" src="https://github.com/user-attachments/assets/0b808d51-a5c4-4726-8089-c2088c574218" />

Apply the changes:
```bash
sudo netplan apply
```

Verification via `ip a`:
<img width="881" height="285" alt="project3" src="https://github.com/user-attachments/assets/2398cacc-0199-4b1a-af9f-5262e900dfc0" />

#### How is this correct?
- The network adapter interface now explicitly displays the assigned static IP `192.168.10.10/24` with `scope global`.
- DHCP is disabled (`dhcp4: no`), ensuring the server retains this address across reboots.
- DNS resolvers (`nameservers: [8.8.8.8, 1.1.1.1]`) are explicitly defined so domain names can be resolved.

---

## 2. Network Troubleshooting & VMware NAT Nuance

In order to verify that our Splunk machine is set up and has external access, we ping an external host:

```bash
ping -c 3 8.8.8.8
```

<img width="450" height="42" alt="image" src="https://github.com/user-attachments/assets/90cce9a7-8af8-4d67-92ab-b613a43e7d83" />

### The Hurdle: "Destination Host Unreachable"
While the tutorial (using VirtualBox) used `192.168.10.1` as the gateway, running ping on my VMware setup returned:
```text
From 192.168.10.10 icmp_seq=1 Destination Host Unreachable
```

#### What I Learned:
- In **VirtualBox NAT networks**, `.1` typically serves as the virtual router/gateway.
- In **VMware Workstation/Player NAT**, the host virtual adapter takes `.1`, while the **actual NAT gateway is `.2`** (`192.168.10.2`).
- Setting `.1` caused the VM to send ARP requests looking for a gateway MAC address that did not exist on that IP, dropping all external traffic.

#### How I Resolved It:
1. Opened VMware **Virtual Network Editor** as Administrator.
2. Selected **VMnet2** (configured to NAT) and set:
   - Subnet IP: `192.168.10.0`
   - Subnet Mask: `255.255.255.0`
   - Gateway IP: `192.168.10.2`
3. Updated Netplan (`/etc/netplan/*.yaml`):
   ```yaml
   routes:
     - to: default
       via: 192.168.10.2
   ```
4. Ran `sudo netplan apply` and re-tested. Outbound ping succeeded with 0% packet loss!

---

## 3. Package Management & APT Lock Contention

Before installing guest tools, running `sudo apt update` returned:
```text
Error: Could not get lock /var/lib/apt/lists/lock. It is held by process 3523 (apt-get)
Error: Unable to lock directory /var/lib/apt/lists/
```

### What I Learned:
- Ubuntu Server triggers background services like `unattended-upgrades` immediately upon boot and network acquisition to look for security updates.
- Because `apt` enforces a single active writer to prevent package database corruption, manual commands fail if a background task holds the lock.

### How I Resolved It:
1. Terminated the lingering process holding the lock (`sudo kill -9 3523`).
2. Cleared leftover lock files and repaired interrupted configurations:
   ```bash
   sudo rm /var/lib/apt/lists/lock
   sudo rm /var/cache/apt/archives/lock
   sudo rm /var/lib/dpkg/lock*
   sudo dpkg --configure -a
   ```
3. Re-ran `sudo apt update`, which completed smoothly.

---

## 4. Shared Folder Setup: VirtualBox vs. VMware

### The VirtualBox Method (From Tutorial)
The tutorial instructions were designed for Oracle VirtualBox:

<img width="352" height="52" alt="image" src="https://github.com/user-attachments/assets/68b8d614-3587-48d9-bc4c-8ca18dc78894" />

Install VirtualBox guest utilities:

<img width="987" height="562" alt="image" src="https://github.com/user-attachments/assets/f66075cb-29b6-4550-8896-bed4d0784594" />

Add user to group:

<img width="440" height="23" alt="image" src="https://github.com/user-attachments/assets/40e9420f-a982-4a75-a762-05e73f09f78d" />

Create directory:

<img width="518" height="242" alt="image" src="https://github.com/user-attachments/assets/dc590a8e-9a16-4f1e-bbcc-79c1ef35d82a" />

The video then mounts using:
```bash
sudo mount -t vboxsf -o uid=1000,gid=1000 AD-Project share/
```

---

### The VMware Adaptation (What Actually Works)

Because `vboxsf` is an Oracle kernel module not present in VMware, running that command fails on VMware. Instead, we use VMware's **HGFS** driver.

#### Step 1: Install VMware Guest Tools
```bash
sudo apt update
sudo apt install open-vm-tools -y
```

#### Step 2: Configure VMware Shared Folders
1. In VMware: **VM** > **Settings** > **Options** tab > **Shared Folders**.
2. Select **Always enabled**.
3. Add a folder from the host OS (e.g., named `AD-Project`).

#### Step 3: Create Mount Point and Mount with `vmhgfs-fuse`
```bash
mkdir -p share
sudo vmhgfs-fuse -o allow_other -o uid=1000,gid=1000 .host:/AD-Project share
```

*(Note: In many VMware configurations, the folder will also automatically appear under `/mnt/hgfs/AD-Project`)*

#### Step 4: Verification
```bash
cd share
ls -la
```

**Verification Output:**

<img width="796" height="178" alt="image" src="https://github.com/user-attachments/assets/800a8c21-5fc6-4ce7-b43d-3618d602150a" />


The share directory is mounted, assigned to our user (`mydfir` / UID 1000), and the Splunk installation `.deb` package is ready for installation.
