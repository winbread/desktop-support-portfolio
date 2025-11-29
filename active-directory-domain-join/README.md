Add Active Directory domain join lab
# Active Directory Lab – Create a Domain and Join a Windows 10 Client

Hands-on lab where I built a small Active Directory environment in VirtualBox:

- Configure a virtual network
- Promote a Windows Server to a Domain Controller
- Create an OU and users in AD
- Join a Windows 10 client to the domain
- Fix a DNS/IP misconfiguration that broke the domain join

---

## Goals

- Practice basic AD DS administration
- Understand how DNS and IP configuration affect domain joins
- Capture a realistic troubleshooting story (not just “happy path”)

---

## Environment

- **Hypervisor:** VirtualBox  
- **Network:** NAT Network (single subnet)  
- **Domain:** `lab.local`  

**Server (DC01)**  
- OS: Windows Server  
- Role: Domain Controller + DNS  
- IP: `192.168.10.10`  
- DNS: `192.168.10.10`  

**Client (WIN10)**  
- OS: Windows 10  
- IP: `192.168.10.20`  
- DNS: `192.168.10.10` (points to DC01)

> Key idea: the client must use the domain controller as its DNS server.

---
## Part 1 – VirtualBox Network Setup

1. In **VirtualBox** go to: `File → Tools → Network Manager`.
2. Create or edit a **NAT Network**:
   - Network: `192.168.10.0/24`
3. Attach both VMs (DC01 and WIN10) to this NAT Network.

**Screenshot idea:** `01-virtualbox-nat-network.png` – NAT network showing `192.168.10.0/24`.

---

## Part 2 – Configure DC01 and Promote to Domain Controller

On **DC01**:

1. Set a static IPv4 address:
   - IP: `192.168.10.10`
   - Mask: `255.255.255.0`
   - Gateway: `192.168.10.1` (or blank)
   - DNS: `192.168.10.10` (itself)
2. Rename the server to `DC01` and reboot.
3. In **Server Manager**, add roles:
   - **Active Directory Domain Services**
   - **DNS Server**
4. Run the AD DS configuration wizard:
   - Create a **new forest**
   - Root domain: `lab.local`
   - Set DSRM password → Next → Install → reboot.

To verify:

```powershell
hostname
whoami
ipconfig /all

```

## Part 3 – Create an OU and Users

On **DC01**:

1. Open **Tools → Active Directory Users and Computers**.
2. Right-click the domain `lab.local` → **New → Organizational Unit**:
   - Name: `HQ_Users`
3. Right-click `HQ_Users` → **New → User** and create accounts, for example:
   - `jdoe` – John Doe  
   - `psmith` – Paula Smith  
   - `alopez` – my own domain user for testing

For this lab I used simple passwords and unchecked:

- **User must change password at next logon**

**Screenshot idea:**  
`04-ad-ou-users.png` – `HQ_Users` OU with the users listed.

---
## Part 4 – Join Windows 10 to the Domain

On **WIN10**:

1. Configure IPv4:
   - IP: `192.168.10.20`
   - Mask: `255.255.255.0`
   - Gateway: `192.168.10.1` (or blank)
   - DNS: `192.168.10.10` (DC01)

2. Test connectivity:
   - Open **Command Prompt** and run:
     ```powershell
     ping 192.168.10.10
     ping dc01.lab.local
     ```

3. Join the domain:
   - Right-click **This PC** → **Properties**
   - Click **Advanced system settings**
   - Go to the **Computer Name** tab → click **Change**
   - Under **Member of**, select **Domain** and enter: `lab.local`
   - Click **OK**

4. When prompted for credentials:
   - Enter a domain account, for example:
     - Username: `LAB\Administrator`
     - Password: (your domain admin password)

5. Reboot the computer when you see the “Welcome to the lab.local domain” message.

After reboot:

- On the login screen, click **Other user**.
- Log in with one of the domain users you created (for example):
  - Username: `LAB\alopez`
  - Password: (password you set)

**Screenshot ideas**

- `05-win10-domain-join.png` – System Properties showing `Domain: lab.local`.  
- `06-win10-logged-in-domain-user.png` – Desktop logged in as `LAB\alopez` (or another domain user).









