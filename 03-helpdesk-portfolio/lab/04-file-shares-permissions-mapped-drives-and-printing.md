# Runbook 04 — File Shares, Permissions, Mapped Drives, Home Drives, and Printing (KevTech Part 7 + Part 12)
**Goal:** I create HR and Personal file shares on my Windows Server, control access using AD security groups + NTFS/share permissions, map network drives for users, configure a per-user home drive, and set up a printer via Print Management.

---

## Prerequisites
- Domain: `practice1.com`
- Server (DC/File server): `Server2025` (use your actual server name consistently)
- Clients: `Desktop1` (admin/RSAT) + `Desktop2` (end-user test machine)
- User: `Patty` in OU `HR`
- Groups: I will create `HR` and `Personal` security groups

> Important: Be consistent with server hostname. If the share is on Server2025, the path must be `\\Server2025\ShareName` (not Server2026).

---

## 1) Create file shares on the Server

### 1.1 Create share: HR
On Server:
1. Open **Server Manager**
2. Go to: **File and Storage Services → Shares**
3. Click: **Tasks → New Share**
4. Choose: **SMB Share – Quick**
5. Name: `HR`
6. Click Next until **Create**

### 1.2 Create share: Personal
Repeat the same process to create a share named: `Personal`

### 1.3 Verify folders exist on disk
By default, these shares map to folders under something like:
- `C:\Shares\HR`
- `C:\Shares\Personal`

I confirm they exist and I can open them.

---

## 2) Create AD security groups to control access
On Desktop1 (or Server) using ADUC (`dsa.msc`):

### 2.1 Create group: HR
1. Go to the OU where I store groups (or use Users if I keep it simple in a lab)
2. Right-click → **New → Group**
3. Name: `HR`
4. Type: **Security**
5. Scope: typically **Global**

### 2.2 Create group: Personal
Repeat:
- Name: `Personal`
- Type: **Security**

> Why groups: I grant folder access to a group, not to individuals. That scales and reduces mistakes.

---

## 3) Document share paths inside AD (helps helpdesk)
### 3.1 Copy the UNC path
On the server:
1. Right-click folder `C:\Shares\Personal` → Properties → Sharing tab (or SMB share details)
2. Copy the network path (UNC), example:
   - `\\Server2025\Personal`
3. Do the same for HR:
   - `\\Server2025\HR`

### 3.2 Paste the path into the group description
In ADUC:
1. Open the group (e.g., `Personal`) → **Description**
2. Paste the UNC path (`\\Server2025\Personal`)
3. Repeat for `HR`

> Purpose: When I troubleshoot a user’s access, I can quickly see “what share is linked to what group”.

---

## 4) Add Patty to the correct groups
In ADUC:
1. Open group `HR` → Members → Add `Patty`
2. Open group `Personal` → Members → Add `Patty`

Verify:
- Open Patty → **Member Of** to confirm membership.

---

## 5) Set permissions (two layers: Share + NTFS)
### Important concept (simple)
- **Share permissions** decide who can connect to the share.
- **NTFS permissions** decide what they can do inside the folder.
- The effective result is the most restrictive combination.

### 5.1 HR folder permissions (NTFS)
On Server:
1. Go to `C:\Shares\HR` → Properties → **Security → Advanced**
2. **Disable inheritance**
3. Remove entries I don’t want (lab: remove overly broad users like “Everyone”)
4. Add:
   - `HR` group: **Modify** (Read/Write)
   - `Helpdesk` (or Helpdesk group): Full control (admin troubleshooting)
   - `Domain Admins`: Full control (optional but typical)

### 5.2 Personal folder permissions (NTFS)
On Server:
1. Go to `C:\Shares\Personal` → Properties → **Security → Advanced**
2. Disable inheritance
3. Add:
   - `Personal` group: Modify (or more restrictive if desired)
   - `Helpdesk`: Full control
   - `Domain Admins`: Full control (optional)

> Note: A “real” home drive setup is usually per-user with `\\Server\Personal\%username%` and special permissions per folder. I configure that in Step 7.

---

## 6) Access test as Patty (UNC path)
On Desktop2, logged in as `PRACTICE1\Patty`:

1. Open File Explorer
2. In the address bar, type:
   - `\\Server2025\HR`
3. Pin to Quick Access if needed.

**Error I hit**
- The folder didn’t show up because I used the wrong server name (`Server2026` vs `Server2025`).

**Fix**
- Use the correct UNC path:
  - `\\Server2025\HR`
  - `\\Server2025\Personal`

---

## 7) Map a network drive (example: HR to Z:)
On Desktop2 (Patty):
1. Open **This PC**
2. Click **Map network drive**
3. Drive letter: `Z:`
4. Folder: `\\Server2025\HR`
5. Check **Reconnect at sign-in**
6. Finish

Now Patty has a persistent drive letter for HR.

---

## 8) Configure a Personal “Home Drive” for Patty (P:)
Purpose: Give Patty a personal drive that maps automatically at logon.

On Desktop1/Server (ADUC):
1. Open Patty → Properties → **Profile**
2. Under **Home folder**:
   - Select: **Connect** `P:` to:
   - `\\Server2025\Personal\%username%`

Result:
- The server will create `\\Server2025\Personal\Patty` (or I create it manually).
- Patty gets a `P:` drive mapped at login.

**Error I hit**
- “Invalid path” because the UNC path started with only one backslash.

**Fix**
- Ensure UNC paths start with double backslash:
  - `\\Server2025\Personal\%username%`

---

# Part 12 — Printing (Print Management) + Directory listing

## 9) Install Print and Document Services
On Server:
1. Server Manager → Manage → Add Roles and Features
2. Select **Print and Document Services**
3. Install

Verify:
- Print Services appear (and Print Management tool is available).

---

## 10) Add a printer in Print Management (lab printer)
On Server:
1. Tools → **Print Management**
2. Print Servers → `Server2025` → Printers
3. Right-click → **Add Printer**
4. Choose method (lab example):
   - **Add a local printer** with a **Local Port** OR
   - Create a TCP/IP port if I have a network printer IP

> In real life, network printers are added via TCP/IP port and a stable IP (often a DHCP reservation by MAC).

---

## 11) Share the printer and publish in AD
On the printer:
1. Printer Properties → **Sharing**
2. Enable **Share this printer**
3. (Optional) **List in the directory**

Why:
- “List in the directory” makes it discoverable to domain users via AD.

Best practice:
- Remove “Everyone” if appropriate and allow only required groups (e.g., `HR`) to use the printer.

---

## 12) Find the published printer in AD (server-side validation)
On Server:
1. ADUC → right-click domain → **Find**
2. Object type: **Printers**
3. Find Now
4. I can connect/manage/remove from here.

---

## 13) Connect as Patty (client-side)
On Desktop2 (Patty):
1. Control Panel → Devices and Printers
2. Add a printer / Add a device
3. The printer should appear because it’s published in the directory and Patty is in HR.

---

## Notes / Lessons learned
- Keep names consistent: server hostname must match in every UNC path.
- File access is “Share permissions + NTFS permissions”. If a user can see a share but can’t write, check NTFS.
- For home drives, use `\\Server\Personal\%username%` and ensure the path starts with `\\`.
