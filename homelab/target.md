# Windows Target Machine Setup & Network Troubleshooting

## Objective
The objective of this phase is to prepare our Windows victim machine (`Target-PC`) to act as the primary log generator for our Active Directory lab. This requires configuring a static IP within our lab subnet, resolving hypervisor-specific routing issues to regain internet access, and verifying HTTP connectivity to the Splunk SIEM web interface hosted on our Ubuntu server.

---

## 1. Setting the Hostname and Static IP

Just like the Ubuntu server, our Windows machine needs to be easily identifiable and statically routed within our internal lab environment so that log forwarding remains consistent.

**Steps Taken:**
1. Renamed the PC to `Target-PC` via **System Properties** and restarted the virtual machine.
2. Navigated to **Network and Internet Settings > Change adapter options**.
3. Right-clicked the network adapter, went to **Properties**, and configured the IPv4 settings to a static IP:
   - **IP Address:** `192.168.10.100` (Assigned a high address so it doesn't conflict with our `.10` Splunk server).
   - **Subnet Mask:** `255.255.255.0`
   - **Default Gateway:** `192.168.10.2`
   - **DNS Server:** `8.8.8.8`

<img width="400" height="455" alt="image" src="https://github.com/user-attachments/assets/89404336-b97d-4fc2-a81c-3fe5bc7d413c" />

---

## 2. Troubleshooting: The VMware Gateway Trap Strikes Back

After setting up the IP address exactly as shown in the video tutorial, I realized I had completely lost internet connectivity on the Windows machine.

#### What I Learned: Hypervisor Networking Differences
I fell into the exact same trap as I did on the Ubuntu machine. The tutorial was filmed using Oracle VirtualBox, where the default NAT virtual router gateway is `192.168.10.1`. However, because my lab is built in **VMware**, the host virtual adapter takes `.1`, and the actual gateway that routes traffic to the outside internet is always **`.2`**.

**The Fix:**
I went back into the IPv4 properties and changed the Windows Default Gateway from `192.168.10.1` to `192.168.10.2`. I also ensured the VM's network adapter in VMware was assigned to the exact same Custom VMnet (e.g., VMnet2 NAT) as my Splunk server.

To verify the fix, I opened the Windows command prompt and tested outbound internet access alongside internal LAN routing:

```cmd
ping 8.8.8.8
ping 192.168.10.10
```


## 3. Installing Splunk Universal Forwarder

The Universal Forwarder is a lightweight agent that collects logs from the Windows machine and streams them to our Splunk Indexer.

**Steps Taken:**
1. Downloaded the 64-bit Windows MSI installer for Splunk Universal Forwarder from the Splunk website.
2. Ran the installer and accepted the license agreement, ensuring I selected the **on-premise** instance option.
3. Created an admin account for the forwarder.
4. **Deployment Server:** Skipped this step since we don't have one in this lab.
5. **Receiving Indexer:** Pointed the forwarder to our Splunk server IP `192.168.10.10` and used the default receiving port `9997` (which we opened on the server earlier).

<img width="490" height="382" alt="image" src="https://github.com/user-attachments/assets/00e1207d-aafe-48e2-b79e-25763f936c0e" />

---

## 4. Installing Sysmon & Olaf's Configuration

By default, Windows Event Logs don't capture deep system activity like process creation, network connections, or file hashing. Sysmon acts as a background service to log this critical telemetry.

#### What I Learned: Why Olaf's Config?
Sysmon can be extremely noisy if we just turn it on without filters. By using a pre-built XML configuration from security researcher [Olaf Hartong](https://github.com/olafhartong/sysmon-modular), we instruct Sysmon to specifically look for known malicious behaviors and filter out benign system noise, saving us indexing space and analysis time.

**Steps Taken:**
1. Downloaded **Sysmon** from Microsoft Sysinternals and extracted the ZIP file.
2. Downloaded Olaf's `sysmonconfig.xml` file from GitHub (saving the raw file).
3. Opened PowerShell as Administrator.
4. Navigated to the extracted Sysmon directory and installed it using the config file:

```powershell
cd C:\Users\Admin\Downloads\Sysmon
.\sysmon64.exe -i ..\sysmonconfig.xml
```

## 5. Configuring inputs.conf

Now that Sysmon and the Forwarder is installed, we have to connect the two. We must tell the Universal Forwarder exactly *which* logs to collect and *where* to send them.

#### What I Learned: Default vs. Local Directories
Splunk stores its configuration files in `C:\Program Files\SplunkUniversalForwarder\etc\system\`. Inside, there are `default` and `local` folders. 
I learned that you should **never** edit files in the `default` directory because system updates will overwrite them. Instead, you always create a new `inputs.conf` file in the `local` directory. Splunk will prioritize the local overrides.

#### Understanding the Configuration
Here is the configuration I pasted into the new file:

```ini
[WinEventLog://Application]
index = endpoint
disabled = false

[WinEventLog://Security]
index = endpoint
disabled = false

[WinEventLog://System]
index = endpoint
disabled = false

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
index = endpoint
disabled = false
renderXml = true
source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operation
```

#### What this does:

- It explicitly tells the forwarder to monitor four key Windows Event Log channels: Application, Security, System, and our newly created Sysmon Operational logs.

- index = endpoint dictates the routing. It ensures all these logs are sent to the specific index we created on the Splunk server earlier, rather than dropping them into Splunk's default main index.

- For the Sysmon logs, renderXml = true is a crucial setting. It formats the event output as XML, which makes it infinitely easier for Splunk to parse out highly specific telemetry fields (like file hashes, command lines, and process GUIDs) later when we start querying for threats.

**Steps Taken:**
1. Opened Notepad as Administrator (required to save files in the Program Files directory).
2. Pasted the custom configuration instructing the forwarder to collect Application, Security, System, and Sysmon logs, and push them to the `endpoint` index we created earlier.
3. Saved the file as `inputs.conf` (selecting "All Files" to prevent a `.txt` extension) in:
   `C:\Program Files\SplunkUniversalForwarder\etc\system\local\`

<img width="810" height="847" alt="inputs" src="https://github.com/user-attachments/assets/5f0fa598-0aac-4629-8930-4c56f9f226c8" />


---

## 6. Fixing Service Permissions & Restarting

Because we updated the `inputs.conf` file, the Splunk Forwarder service needs to be restarted to read the new rules. However, there is a critical permission issue to fix first.

#### What I Learned: Service Logon Permissions
By default, the Splunk Forwarder service runs under a restricted `NT SERVICE\SplunkForwarder` account. This account often lacks the necessary Windows permissions to read deep system and security event logs. We have to change it to the `Local System` account so it has full access to the telemetry we want to collect.

**Steps Taken:**
1. Opened the Windows **Services** app as Administrator.
2. Located the **SplunkForwarder** service and opened its Properties.
3. Went to the **Log On** tab and changed the setting from the specific service account to **Local System account**.
4. Applied the changes and restarted the SplunkForwarder service. *(Note: If Windows throws a warning saying it can't stop the service, just acknowledge it and hit start).*

<img width="802" height="392" alt="splunkrunsaslocalsystem" src="https://github.com/user-attachments/assets/61397461-5944-45a9-80f9-95b8586891a6" />
