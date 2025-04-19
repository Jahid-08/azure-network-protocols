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
