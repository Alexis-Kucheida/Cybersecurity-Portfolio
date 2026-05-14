# Runbook — PDQ Deploy + PDQ Inventory (KevTech Part 10 + Part 11)

## Objective
I install **PDQ Deploy** and **PDQ Inventory** on `Server2025` and use them to:
1) Deploy software packages (example: Zoom) to multiple computers (`Server2025`, `Desktop1`, `Desktop2`)
2) Inventory computers to review installed apps, OS version, updates, hardware, and run reports

---

## Lab context (my setup)
- Domain: `practice1.com`
- Domain Controller / PDQ host: `Server2025`
- Workstations: `Desktop1`, `Desktop2`
- Accounts:
  - Setup/admin: `PRACTICE1\Administrator`
  - Helpdesk (optional): `PRACTICE1\Helpdesk`

---

## Corrections to my notes (important)
- I keep hostnames consistent: `Server2025` (not Server25/Server26).
- I only switch to internet networking temporarily (Bridged + DHCP) to download installers and dependencies. I restore lab networking afterward (Host-Only/Internal + static IP plan + DNS pointing to Server2025).
- PDQ deployments/inventory require reachability and admin access (SMB/admin shares, remote services). If targets are unreachable, I troubleshoot networking/permissions first.

---

# Part A — Transfer installers into Server2025 using a VirtualBox shared folder

## A1) Prepare a host folder for installers
On my host PC:
1) Create a folder:
   - `C:\Downloads\Alexis_Lab`
2) Download installers on the host and place them in that folder:
   - `PDQDeploy.exe`
   - `PDQInventory.exe`

## A2) Mount the shared folder in Server2025 (VirtualBox)
On the `Server2025` VM window:
1) VirtualBox menu → Devices → Shared Folders → Shared Folder Settings
2) Add a new shared folder:
   - Folder Path: `C:\Downloads\Alexis_Lab`
   - Auto-mount: enabled
   - Make Permanent: optional
3) Reboot `Server2025` if the share does not appear immediately.

Verification inside `Server2025`:
- The shared folder appears as `\\VBOXSVR\Alexis_Lab` or as a mounted drive letter.

---

# Part B — Temporarily give Server2025 internet access (for downloads/.NET dependencies)

## B1) Switch Server2025 VM to Bridged Adapter (temporary)
On the host (VirtualBox settings for `Server2025`):
1) Settings → Network → Adapter 1
2) Attached to: Bridged Adapter
3) Apply/OK

## B2) Set Server2025 IPv4 to DHCP (temporary)
On `Server2025`:
1) Control Panel → Network and Internet → Network and Sharing Center
2) Change adapter settings → Ethernet → Properties
3) IPv4 → Properties
4) Select:
   - Obtain an IP address automatically
   - Obtain DNS server address automatically
5) OK

Verify internet on `Server2025`:
    ping 8.8.8.8

---

# Part C — Install PDQ Deploy on Server2025

## C1) Install from shared folder
On `Server2025`:
1) Open the shared folder:
   - `\\VBOXSVR\Alexis_Lab`
2) Run:
   - `PDQDeploy.exe`
3) Complete installation
4) Launch PDQ Deploy

Note: Internet may be required during install for prerequisites like .NET.

---

# Part D — Deploy a software package (example: Zoom)

## D1) Confirm targets exist in Active Directory
On `Server2025`:
1) Open ADUC (`dsa.msc`)
2) Confirm computer objects exist:
   - `Server2025`
   - `Desktop1`
   - `Desktop2`

## D2) Deploy Zoom with PDQ Deploy
On `Server2025` in PDQ Deploy:
1) Go to Packages
2) Select a package (example: Zoom Client)
3) Choose Deploy Once (or equivalent)
4) Choose Targets
5) Select targets via:
   - Active Directory → Computers
6) Select:
   - `Server2025`, `Desktop1`, `Desktop2` (as needed)
7) Start deployment

## D3) Verify install on targets
On a target machine (example `Desktop2`):
- Confirm Zoom appears in Apps/Programs
- Launch Zoom to validate

---

# Part E — Restore lab network configuration (after installs)

## E1) Switch Server2025 VM back to lab network (Host-Only/Internal)
On the host (VirtualBox settings for `Server2025`):
1) Settings → Network → Adapter 1
2) Attached to: Host-Only Adapter (or your lab Internal Network)
3) Apply/OK

## E2) Restore Server2025 static IP and DNS (lab baseline)
On `Server2025`:
1) Ethernet → Properties → IPv4 → Properties
2) Restore the static lab IP plan (example pattern):
   - IP: `10.1.10.2`
   - Subnet mask: `255.255.255.0` (adjust if your lab differs)
   - DNS: `10.1.10.2`
3) OK

---

# Part F — Install PDQ Inventory on Server2025

## F1) Install from shared folder
On `Server2025`:
1) Open:
   - `\\VBOXSVR\Alexis_Lab`
2) Run:
   - `PDQInventory.exe`
3) Complete installation
4) Launch PDQ Inventory

---

# Part G — Use PDQ Inventory (apps, OS, updates, hardware, reports)

## G1) Confirm computers appear
On `Server2025` in PDQ Inventory:
1) Go to All Computers
2) Confirm `Desktop1` and `Desktop2` appear
   - If not, run the tool’s discovery/scan feature (method depends on PDQ version)

## G2) Run reports on a target computer
Example workflow:
1) All Computers → select `Desktop2`
2) Run reports / view information such as:
   - Applications
   - Services
   - Shared Folders
   - OS Version
   - Windows Updates
   - Hardware Inventory

---

# Part H — Operational notes (what I can do from PDQ tools)
From PDQ (depending on permissions and configuration), I can:
- Deploy packages silently to computers (even while users are logged in)
- See which software and updates are installed
- Run inventory reports for troubleshooting
- Use remote actions as available (restart, remote tools, etc.)

---

## Validation checklist (done criteria)
- PDQ Deploy launches on `Server2025`
- A test package (Zoom or similar) successfully installs on at least one target (`Desktop2`)
- PDQ Inventory shows `Desktop1` and `Desktop2` under All Computers
- I can run at least one report on `Desktop2` (Applications or OS Version)
- After installations, lab networking is restored (Host-Only/Internal + static IP plan + DNS pointing to `Server2025`)
