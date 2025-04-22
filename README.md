<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />


<h2>Video Demonstration</h2>

- ### [YouTube: Azure Virtual Machines, Wireshark, and Network Security Groups](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Create Resource Groups and Virtual Machines in Azure
- Connect to virtual Machines and use Wireshark and view ICMP packet captures between them
- Configure Firewalls and observing traffic


<h2>Actions and Observations</h2>

<p>
<img width="633" alt="image" src="https://github.com/user-attachments/assets/6fc8ba30-cd30-49d3-ab38-097dfc05930c" />
</p>
<p>

# Lab: Performing Activities on the Network  
## Part 1 – Creating Virtual Machines in Azure

### Step 1: Create a Resource Group

To begin the lab, we need to create the virtual machines that will help us test and observe network traffic. Our first task is to set up a **Windows 10 virtual machine**.

1. Navigate to [portal.azure.com](https://portal.azure.com).
2. In the search bar at the top, type and select **"Resource groups"**.
3. Click **"Create"** to make a new resource group.
4. Set the following configuration:
   - **Resource Group Name:** `RG-Network-Activities`
   - **Region:** `East US 2`
5. Click **"Review + Create"**, then confirm by clicking **"Create"**.

> 💡 *Creating a resource group is like making a folder—it helps keep all of your Azure resources organized under one logical group.*</p>
<br />
<br />
<br />

<p>
<img width="701" alt="image" src="https://github.com/user-attachments/assets/85af2fcc-5a9c-475e-b8c1-6e504208e71f" />
</p>

### 🖥️ Step 2: Create the Windows 10 Virtual Machine

Now we’ll set up the **Windows 10 VM** that will be used to observe and test network traffic.

---

#### 🔍 Navigate to Virtual Machines

1. Go to [https://portal.azure.com](https://portal.azure.com).
2. In the search bar, type **"Virtual machines"** and click on the result.
3. Click **➕ Create** to start a new virtual machine setup.

---

#### ⚙️ Configuration Settings

- **Resource Group:** `RG-Network-Activities`
- **Virtual Machine Name:** `windows-vm`
- **Region:** `East US 2`
- **Image:** `Windows 10`
- **Size:** Choose a size with at least **2 vCPUs**
- **Username:** `labuser`
- **Password:** `Cyberlab123!`
- ✅ **Check the box** for Windows licensing

---

#### 🌐 Networking Setup

1. Click **Next** until you reach the **Networking** tab.
2. Under **Virtual Network**, click **Create new**
   - **Name:** `Network-Activities-Vnet`
3. Click **OK**

---

#### 🚀 Final Steps

- Click **Review + Create**
- Then click **Create** to deploy your VM

> 💡 *Make sure your VM is placed in the same region and resource group to keep things organized!*<p>
</p>
<br />
<br />
<br />


<p>
<img width="719" alt="image" src="https://github.com/user-attachments/assets/f8c459d6-afcc-457c-b77a-3a13fd1c0f10" />
</p>
<p>

### 🐧 Step 3: Create the Linux (Ubuntu) Virtual Machine

Next, we'll create a **Linux (Ubuntu)** VM to interact with our Windows VM in the same network.

---

#### 🔍 Navigate to Virtual Machines

1. In the Azure portal, go to **"Virtual machines"** again.
2. Click **➕ Create** to begin setting up your second VM.

---

#### ⚙️ Configuration Settings

- **Resource Group:** `RG-Network-Activities` (same as before)
- **Virtual Machine Name:** `linux-vm`
- **Region:** `East US 2`
- **Image:** `Ubuntu Server 22.04 LTS - x64 Gen2`
- **Authentication Type:** `Password`
- **Username:** `labuser`
- **Password:** `Cyberlab123!`
- ❌ No need to check any licensing box

---

#### 🌐 Networking Setup

- Under **Virtual Network**, select:
  - `Network-Activities-Vnet` (same VNet used for the Windows VM)

---

#### 🚀 Final Steps

- Click **Review + Create**
- Then click **Create** to deploy your Ubuntu VM

> 💡 *It's important that both VMs share the same Resource Group and Virtual Network so they can communicate with each other during the lab.*</p>
<br />
<br />
<br />


<p>
<img width="957" alt="image" src="https://github.com/user-attachments/assets/92b4578c-e629-4f98-8f7c-db67d3da9ab7" />
</p>
<p>

### 🔐 Step 4: Connect to the Windows VM via Remote Desktop (RDP)

Now that both VMs are created, we’ll connect to the **Windows VM** using **Remote Desktop** so we can begin inspecting **ICMP traffic**.

---

#### 🧭 Find the Public IP Address

1. In the Azure portal, navigate to **Virtual Machines**.
2. Select your VM: `windows-vm`
3. Copy the **Public IP address** listed in the VM overview.

---

#### 🖥️ Connect via RDP

1. On your local Windows machine, open the **Start Menu**.
2. Type and open **"Remote Desktop Connection"** (or just `RDP`).
3. Paste the **public IP address** into the Computer field.
4. Click **Connect**.
5. When prompted, log in with:
   - **Username:** `labuser`
   - **Password:** `Cyberlab123!`

---

#### 🧪 Install WireShark

Once you're inside the Windows VM:

1. Open a browser and go to [https://www.wireshark.org/](https://www.wireshark.org/)
2. Download and install **WireShark**.
3. We’ll use this tool to inspect **ICMP (ping)** traffic between the Windows and Linux VMs.

> 💡 *WireShark is a powerful packet analysis tool that allows us to see what's happening on the network in real-time.*
</p>
<br />
<br />
<br />


<p>
<img width="941" alt="image" src="https://github.com/user-attachments/assets/60eb0523-b147-481e-91d1-fca497624565" />
</p>
<p>

### 🧪 Step 5: Capture ICMP Traffic with Wireshark

Now we’ll begin analyzing **ICMP traffic** (ping requests/replies) using **Wireshark** on the Windows VM.

---

#### ▶️ Start a Packet Capture in Wireshark

1. On your Windows VM, open **WireShark**.
2. Click on the active **Ethernet interface** (usually the one with activity bars).
3. Click the **🦈 shark fin icon** in the top-left corner to begin capturing packets.

> 🧠 You’ll now see a stream of real-time network traffic — don’t worry if it looks overwhelming!

---

#### 🔍 Filter for ICMP Traffic

1. At the top of the Wireshark window, find the **display filter bar**.
2. Type:  icmp

This will filter for only **ICMP traffic** (used by the `ping` command).

> ⚠️ It might be empty right now — that's because we haven’t pinged anything yet!

---

#### 🌐 Retrieve the Private IP of the Linux VM

1. In Azure, go to the **Virtual Machines** section.
2. Click on `linux-vm`
3. Go to the **Networking** tab and locate the **Private IP address**  
- 📌 In our case: `10.1.0.5`

---

#### 🏓 Ping the Linux VM from Windows

1. On your Windows VM, open **PowerShell**.
2. Type the following command: ping 10.1.0.5

You should see successful replies in PowerShell.

💥 In Wireshark, ICMP traffic will now appear:

Ping requests from the Windows VM’s IP

Ping replies from the Linux VM’s IP

💡 Ping is a simple yet powerful tool to verify network connectivity and measure response times between devices in an IP network.

</p>
<br />
<br />
<br />


<p>
<img width="946" alt="image" src="https://github.com/user-attachments/assets/1a11d2f0-8cae-422e-a9da-af8974613824" />
</p>
<p>

### 🔎 Step 6: Inspect ICMP Packet Details in Wireshark

Now that we’ve captured ICMP traffic between the Windows and Linux VMs, let’s dig into the packet structure to understand what’s really happening under the hood.

---

#### 🧬 Expand Packet Headers in Wireshark

When you click on a captured packet in Wireshark, you’ll see several collapsible sections (headers). Here's what each layer reveals:

---

#### 📡 **Ethernet (Layer 2)**

- Shows the **Source MAC Address** (Windows VM)
- Shows the **Destination MAC Address** (Linux VM)
- This is the **hardware-level** communication between devices

---

#### 🌍 **Internet Protocol (Layer 3)**

- Displays the **Source IP Address**
- Displays the **Destination IP Address**
- This is how devices identify and route across networks

---

#### 💬 **ICMP Section**

- Shows the **payload data** that was sent
- Example: You’ll see a message like `32 bytes of data`
- In the **right-hand panel**, you’ll find the actual raw data sent. In our case it begins with something like `abcd...`

---

> 💡 In tools like PowerShell, you only see basic ping responses. But in **Wireshark**, you can dive deep into the **packet structure**, view **payloads**, and inspect traffic in a way that's incredibly useful for network troubleshooting and analysis.

> 🔍 *Exploring just the payload is only scratching the surface — Wireshark can uncover tons of detailed information for every protocol and interaction happening across your network.*
</p>
<br />
<br />
<br />


<p>
<img width="934" alt="image" src="https://github.com/user-attachments/assets/d0367f6a-406b-4450-aeeb-e1f98ccc2ab7" />
</p>
<p>

### 🔥 Step 7: Configure a Firewall to Block ICMP Traffic

In this step, we’ll simulate a network restriction by blocking **ICMP traffic** (ping) using a firewall rule on the `linux-vm`. We’ll observe how this impacts communication between the two VMs using **Wireshark** and **PowerShell**.

---

#### 🏓 Initiate a Non-Stop Ping from Windows to Linux

1. On your Windows VM, open **PowerShell**.
2. Start an infinite ping to the Linux VM:
   ```powershell
   ping 10.1.0.5 -t

3. You should see continuous ping requests and replies.

4. In Wireshark, ensure your filter is still set to: **icmp**

🔁 This creates a non-stop exchange of ICMP traffic that you’ll see clearly in both Wireshark and PowerShell.

#### 🛡️ Block ICMP Traffic Using Azure Firewall (NSG)

Now we’ll block ICMP traffic from reaching the Linux VM by configuring an **Inbound Security Rule**.

---

##### 🧭 Steps to Access the Firewall (NSG)

1. Go to the **Azure Portal**
2. Navigate to:
   - **Virtual Machines** > `linux-vm` > **Networking`
3. Under **Network security group**, click the listed NSG (e.g., `linux-vm-nsg`)
4. In the left-hand menu, go to:
   - **Settings** > **Inbound security rules**
5. Click **➕ Add** to create a new firewall rule

---

##### ⚙️ Configure the Inbound Rule

Set the following values:

- **Source:** `Any`
- **Destination:** `Any`
- **Destination port ranges:** `*` (means "any port")
- **Protocol:** `ICMPv4`
- **Action:** `Deny`
- **Priority:** `290`
- **Name:** `Deny-ICMP`

Click **Add** to apply the rule.

> 🚫 Once this rule is active, ICMP (ping) traffic coming **into** the Linux VM will be blocked.

#### ✅ Re-Enable ICMP Traffic

Now we’ll remove the firewall block to allow ICMP (ping) traffic to flow again.

---

##### 🔄 Delete the Firewall Rule

1. Go back to the **Inbound security rules** section of your `linux-vm-nsg`.
2. Locate the rule you created (e.g., `Deny-ICMP`).
3. Click **Delete** to remove the rule.

---

##### 📶 Verify Connectivity is Restored

- In **PowerShell**, ping responses will resume — no more timeouts.
- In **Wireshark**, you'll once again see ICMP **request and reply** packets.

---

#### 🛑 Stop the Infinite Ping

To stop the continuous ping in **PowerShell**, press: **Ctrl + C**

> 💡 ICMP traffic is now unblocked, and communication between your VMs is fully restored.



</p>
<br />
<br />
<br />


<p>
<img width="810" alt="image" src="https://github.com/user-attachments/assets/aa8609ea-ce71-4213-99af-0e5c232f11cf" />
<img width="728" alt="image" src="https://github.com/user-attachments/assets/df3eeb28-952c-4264-9532-badc0fed13ca" />
</p>
<p>

### 🔐 Step 8: Observe SSH Traffic in Wireshark

Now we’re going to observe **SSH traffic**, which is a secure protocol used to access and manage remote systems over an unsecured network.

---

#### 🔒 What is SSH?

**SSH (Secure Shell)** encrypts the connection to ensure **privacy and data integrity** when accessing a remote system. It uses **port 22** by default and prevents sensitive data from being transmitted in plain text.

---

#### 📡 Start Capturing SSH Traffic in Wireshark

1. On your **Windows VM**, open **Wireshark**.
2. In the **filter bar**, type: **ssh**
3. 3. Press **Enter** — the screen will likely be empty for now, since no SSH connection has occurred yet.

---

#### 🧑‍💻 SSH into the Linux VM

1. Open **PowerShell** on the Windows VM.
2. Type the following command to SSH into the Linux VM:
   
```powershell
ssh labuser@10.1.0.5
```
You’ll be prompted with a yes/no confirmation to connect.

Enter yes, then enter the password:Cyberlab123!

#### 👀 What You’ll See

- **SSH traffic** will immediately appear in **Wireshark**, even before logging in.
- In **PowerShell**, the interface will change once you're inside the `linux-vm`.
- Every command or keystroke typed will generate **encrypted SSH packets** in Wireshark.

> 🔐 Since SSH encrypts data, you won't be able to see the **contents** of the payload — only that data is being transferred.

---

#### 📊 Dive Deeper into SSH Packets

1. In Wireshark, click on one of the SSH packets.
2. Expand the **Transmission Control Protocol (TCP)** section.
3. You'll observe:
   - **Source Port**
   - **Destination Port**
   - Flags and control information

> 🧠 The **destination port** is `22` when traffic is sent **to** the Linux VM.  
> When the Linux VM responds, **port 22 becomes the source** (replying from SSH).
> If you want to exit the linux terminal simply type **exit** and you will be taken back to regular PowerShell.

---

> 💡 This is a simple demonstration of how SSH works — allowing secure, encrypted communication between devices across an unsecured network.

</p>
<br />
<br />
<br />


<p>
<img width="807" alt="image" src="https://github.com/user-attachments/assets/e19d6d6c-8102-41f0-b985-51ac9cab843e" />
</p>
<p>

### 📦 Step 9: Observe DHCP Traffic in Wireshark

In this step, we’ll observe **DHCP traffic** using Wireshark by releasing and renewing our Windows VM’s IP address — which will trigger the full **DORA** process.

---

#### ❓ What is DHCP?

**DHCP (Dynamic Host Configuration Protocol)** is a network protocol that automatically assigns IP addresses and other configuration details (like DNS and gateway info) to devices on a network.

---

#### 🧪 Set Up DHCP Filtering in Wireshark

1. In **Wireshark**, clear any existing filters.
2. In the filter bar, type: **dhcp**
3. Press **Enter** to begin filtering for DHCP traffic.

---

#### 📝 Create a Batch File to Run DHCP Commands

We’ll use a script to run the release/renew process smoothly and ensure all **DORA** steps are captured.

1. Open **Notepad**
2. Go to: `File → Save As`
3. In the save dialog:
- Navigate to: `This PC > C:\ProgramData`
- Set **File Name:** `dhcp.bat`
- Set **Save as type:** `All Files (*.*)`

4. Inside the notepad, type the following commands:
```bat
ipconfig /release
ipconfig /renew
```
5. Save the file.

> 🧾 A `.bat` (batch) file allows multiple commands to be executed in sequence — great for automating tasks like this.

---

#### 📁 Run the Batch Script

1. Open **PowerShell** on the Windows VM.
2. Change directory to where the script is saved:
   ```powershell
   cd C:\ProgramData
Type **ls** to confirm the dhcp.bat file is there.

Run the script: **.\dhcp.bat**
⚠️ Running this script will temporarily kill your network connection, but it will quickly return once the DHCP lease is renewed.

#### 🔁 What is the DORA Process?

When a device needs an IP address, it goes through the **DORA** process:

- **D – Discover**:  
  The device sends out a broadcast message looking for available DHCP servers.

- **O – Offer**:  
  A DHCP server replies with an offer, which includes an available IP address and configuration details.

- **R – Request**:  
  The device sends a request back to the server, asking to use the offered IP address.

- **A – Acknowledge**:  
  The DHCP server sends an acknowledgment, officially assigning the IP address to the device.

> 📡 This entire sequence can be observed in Wireshark when the batch script is executed.

✅ Confirm the Result
Once your VM reconnects, go back to PowerShell.

Type: **ipconfig** and view the IP address.

💡 In **Wireshark**, you can expand each DHCP packet to view detailed information — such as transaction IDs, lease times, and server identifiers. This level of visibility is why Wireshark is such a valuable tool for troubleshooting and learning how network protocols operate behind the scenes.



   </p>
<br />
<br />
<br />


<p>
<img width="858" alt="image" src="https://github.com/user-attachments/assets/dc81954d-0a31-4d71-b08e-5428414edce7" />
</p>
<p>

### 🌐 Step 10: Observe DNS Traffic in Wireshark

Now we’ll analyze **DNS traffic** — the system that translates website names into IP addresses — using Wireshark.

---

#### ❓ What is DNS?

**DNS (Domain Name System)** is like the **phonebook of the internet**. It translates **human-readable domain names** (like `disney.com`) into **IP addresses** that computers use to locate and communicate with each other.

---

#### 🔍 Filter DNS Traffic in Wireshark

1. Open **Wireshark** on your Windows VM.
2. In the filter bar, type: **dns**
3. Press **Enter** — unlike some other filters, you may immediately see traffic as the VM communicates with its DNS server.

---

#### 🧪 Generate DNS Traffic Using `nslookup`

Let’s now generate DNS queries manually using PowerShell.

---

#### ❓ What is `nslookup`?

**`nslookup`** is a command-line tool used to **query DNS servers** for information about domain names or IP addresses.

---

#### 💻 Run a DNS Query in PowerShell

1. Open **PowerShell**.
2. Type the following command:
```powershell
nslookup disney.com
```
3. You should see one or more **IP addresses** returned for `disney.com`.

---

#### 📡 What You’ll See in Wireshark

- Wireshark will display the **DNS query** sent from your VM to the DNS server.
- You’ll also see the **DNS response**, which includes the resolved IP address(es) for `disney.com`.

> 💡 DNS makes it possible to visit websites by name rather than memorizing complex IP addresses — just like using a contact name in your phone instead of dialing the number directly.
</p>
<br />
<br />
<br />


<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
<br />
<br />


<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
<br />
<br />


<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
<br />
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
<br />
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
<br />
<br />
