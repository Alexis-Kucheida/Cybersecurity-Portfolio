# Runbook — KevTech Helpdesk Lab (Videos 1–4)
**Goal:** I build a basic helpdesk lab in VirtualBox with Windows Server 2025 as a Domain Controller (`practice1.com`) and a Windows 11 Pro workstation joined to the domain. I also install RSAT on Windows 11 so I can administer AD/DNS/DHCP/GPO tools from the client.

---

## 0) Lab Design (what I’m building and why)

### What each machine is for
- **Server 2025 (DC):** runs Active Directory Domain Services (AD DS) + DNS (and optionally DHCP). This is my identity and authentication “source of truth”.
- **Windows 11 Pro (Client):** joins the domain and acts as my “helpdesk workstation”, with RSAT tools installed.

### Important concept: DNS vs Default Gateway
- **DNS is mandatory** for domain join. The Windows 11 client must use the **Domain Controller IP as DNS** so it can find AD services.
- The **default gateway is not the DC** unless the DC is configured as a router (not the case in this lab). In Host-Only/Internal labs, I can often leave the gateway blank.

---

## 1) Install Windows 11 + Install Server 2025

### 1.1 Install Windows 11 (VirtualBox)
1. I create a Windows 11 VM in VirtualBox and install Windows 11 **Pro**.
2. After install, I install **Guest Additions** (clipboard, resizing, better drivers).

### 1.2 Install Server 2025 (VirtualBox)
1. I create a Server 2025 VM and boot from the Server 2025 ISO.
2. During setup, I select the correct edition.

**Error I made**
- I selected the wrong installer option/edition.

**Fix**
- I select exactly **Windows Server 2025 Standard (Desktop Experience)** (or the intended Server 2025 edition with GUI).

3. After setup finishes, I eject the ISO and set the Administrator password.
4. I log in.

---

## 2) Rename Server + Install AD DS + Promote to DC

### 2.1 (Optional) Performance optimization
1. I open: `This PC → Properties → Advanced system settings → Performance`
2. I select: **Adjust for best performance**.

### 2.2 Install AD DS
1. I open **Server Manager**.
2. I go to: `Manage → Add Roles and Features`.
3. I choose: **Role-based or feature-based installation**.
4. I select my server.
5. I check: **Active Directory Domain Services (AD DS)**.
6. I click **Install**.

> Note: AD DS is core infrastructure for identity, authentication, authorization, and domain-based administration.

### 2.3 Promote the server to a Domain Controller
1. In Server Manager, I click the notification flag and select **Promote this server to a domain controller**.
2. I choose: **Add a new forest**.
3. Root domain name: `practice1.com`
4. I set the DSRM password.
5. I complete the wizard and let the server restart.

### 2.4 Validate AD is working
1. I open: `Server Manager → Tools → Active Directory Users and Computers (ADUC)`
2. I verify I can browse: `practice1.com → Users`

---

## 3) Create Accounts + AD Recycle Bin + Useful Commands

### 3.1 Create a user (standard way)
1. I open ADUC: `Server Manager → Tools → ADUC`
2. I go to: `practice1.com → Users`
3. Right-click → `New → User`
4. I create a user (example: `helpdesk`).

### 3.2 Create a Helpdesk account using “Copy”
1. In ADUC → Users
2. I right-click an existing user (e.g., an admin template user) → **Copy**
3. I name the new user: **Helpdesk**.

### 3.3 Enable AD Recycle Bin (recommended)
1. I open: `Windows Administrative Tools → Active Directory Administrative Center`
2. I select my domain `practice1.com`
3. I enable **Recycle Bin**

> Purpose: I can recover accidentally deleted objects (users/OUs) instead of losing progress.

### 3.4 Commands I learned (clarified)
- `ipconfig` = basic network configuration (IP, gateway, DNS)
- `ipconfig /all` = full details (DHCP enabled, DNS suffix, MAC, etc.)
- `net user` = lists local users on the machine
- `net user helpdesk /domain` = shows domain user details

**AI Correction**
- `net user` does not show “shared drives”. For shares I would use `net share` (system-level shares) or check group membership and file share permissions separately.

---

## 4) Static IP + Join Domain + RSAT + Troubleshooting

## 4.1 Configure Server 2025 with a static IP (DC best practice)
1. I open: `Control Panel → Network and Internet → Network and Sharing Center`
2. I click: `Change adapter settings`
3. Right-click `Ethernet → Properties`
4. Select `Internet Protocol Version 4 (TCP/IPv4) → Properties`
5. I set:
   - IP address: `10.1.10.2`
   - Subnet mask: (my lab subnet, commonly `255.255.255.0`)
   - DNS: `10.1.10.2`

> Note: I only set a default gateway if I actually have a router on that network.

## 4.2 VirtualBox network configuration
1. In VirtualBox settings for both VMs, I set the lab adapter to:
   - **Host-Only Adapter** (same host-only network on both VMs)

**Error I made**
- I left one VM in **NAT** and the other in **Host-Only**, so they couldn’t communicate.

**Fix**
- I make both VMs use the same Host-Only adapter for the lab network.

## 4.3 Windows 11 local admin account handling (what I tried)
I attempted to enable/modify local accounts in Windows 11.

**Note**
- Some GUI paths differ by edition and policy. Since I use Windows 11 Pro, I can use Local Users and Groups, but I also learned command line methods.

Useful commands:
- Create local user:
  - `net user <username> <password> /add`
- Add to local administrators:
  - `net localgroup administrators <username> /add`
- Delete local user:
  - `net user <username> /delete`
- Disable an account:
  - `net user <username> /active:no`

## 4.4 Install RSAT on Windows 11 (Helpdesk workstation tools)
Goal: I install these RSAT components:
- Active Directory tools (ADUC)
- DNS tools
- DHCP tools
- Group Policy Management tools (GPMC)
- Remote Desktop Services tools
- Server Manager

### What went wrong and why
**Problem**
- RSAT installs as Windows “Capabilities” and needs a working content source.
- My VM had issues using Windows Update/online content, and I was on build `26200`.
- Some RSAT items failed, stayed “Staged”, or would not install.

**Solution**
- I mounted the matching **Languages and Optional Features** ISO and installed RSAT using PowerShell with an offline source.

### RSAT install pattern (offline source)
1. I mount the ISO in VirtualBox (Optical Drive) and confirm the drive letter (example: `D:`).
2. I install capabilities using:
   - `Add-WindowsCapability -Online -Name <capability> -Source "D:\LanguagesAndOptionalFeatures" -LimitAccess`
3. I reboot as needed until items move from **Staged** to **Installed**.

Verification commands:
- Check RSAT states:
  - `Get-WindowsCapability -Online -Name RSAT* | Select Name, State`
- Open tools:
  - ADUC: `dsa.msc`
  - DNS: `dnsmgmt.msc`
  - DHCP: `dhcpmgmt.msc`
  - GPMC: `gpmc.msc`

## 4.5 Join Windows 11 to the domain `practice1.com`

### Step A — Set Windows 11 client IP + DNS
1. On Windows 11, I set IPv4 to:
   - IP: `10.1.10.3`
   - Subnet mask: (same as server)
   - DNS: `10.1.10.2` (Domain Controller)

### Step B — Test connectivity and name resolution (before joining)
1. I test:
   - `ping 10.1.10.2`
2. I validate DNS:
   - `nslookup practice1.com`
   - `nslookup _ldap._tcp.dc._msdcs.practice1.com`

If ping fails, I check VirtualBox network mode (Host-Only on both VMs).

### Step C — Rename client and join domain
1. I rename the PC to `Desktop1` (optional but good practice).
2. I join the domain:
   - `System Properties → Computer Name → Change → Domain: practice1.com`
3. When prompted, I enter **domain credentials** (not local):
   - `PRACTICE1\Administrator` or `Administrator@practice1.com`
4. I restart.

### Step D — Validate join succeeded
1. On the Server, I open ADUC.
2. I check: `practice1.com → Computers`
3. I confirm `Desktop1` appears.

---

## 5) Final State (what “done” looks like)
- Server 2025 is a Domain Controller for `practice1.com`
- I can open ADUC and manage Users/Computers
- I created a `Helpdesk` domain user
- Windows 11 is joined to the domain as `Desktop1`
- RSAT tools open on Windows 11 (ADUC/DNS/DHCP/GPMC)

---

## 6) Common Mistakes and Fixes (quick reference)

### Domain join fails even with correct credentials
- Fix: On Windows 11, set **DNS = DC IP** (e.g., `10.1.10.2`). Domain join depends on DNS.

### Ping fails between VMs
- Fix: Ensure both VMs are on the same VirtualBox network (Host-Only/Internal), not mixed with NAT.

### RSAT fails to install / stays “Staged”
- Fix: Use the matching **Languages and Optional Features** ISO as the content source and reboot until it becomes Installed.
