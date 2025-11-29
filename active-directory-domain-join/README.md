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

1. In **VirtualBox**: `File → Tools → Network Manager`.
2. Create/edit a **NAT Network**:
   - Network: `192.168.10.0/24`
3. Attach both VMs (DC01 and WIN10) to this network.

**Planned screenshot:**  
`01-virtualbox-nat-network.png` – NAT network using `192.168.10.0/24`.

---

## Part 2 – Configure DC01 and Promote to Domain Controller

On **DC01**:

1. Set a static IPv4 address:
   - IP: `192.168.10.10`
   - Mask: `255.255.255.0`
   - Gateway: `192.168.10.1` (or blank if no internet)
   - **DNS:** `192.168.10.10` (itself)
2. Rename the server to `DC01` and reboot.
3. In **Server Manager**, install:
   - **Active Directory Domain Services**
   - **DNS Server**
4. Promote to a **new forest**:
   - Root domain: `lab.local`
   - Set DSRM password → Next → Install → reboot.

**Planned screenshots:**

- `02-dc01-network-settings.png` – IPv4 properties on DC01.  
- `03-dc01-ipconfig-all.png` – `ipconfig /all` after promotion.

Commands used:

```powershell
hostname
whoami
ipconfig /all


