# Runbook 02 — Group Policy + RSOP + Second Domain PC (KevTech Part 5)
**Goal:** I add a second Windows 11 workstation (Desktop2) to my `practice1.com` domain, organize users with OUs and security groups, configure basic security settings in the Default Domain Policy, and validate policies using RSOP and command-line tools.

---

## Prerequisites
- Domain Controller is running: `practice1.com` (Server 2025)
- Desktop1 is already domain-joined and has RSAT tools working
- Network: all lab VMs are on the same Host-Only/Internal network
- DNS: clients point to the DC IP (example: `10.1.10.2`)

---

## 1) Create the 2nd workstation VM (Desktop2)
1. In VirtualBox I create a second Windows 11 VM named **Desktop2**.
2. I ensure its network adapter is on the same lab network as the server (Host-Only/Internal).
3. I install Windows and log in locally for initial setup.

**Common mistake**
- If Desktop2 is left in NAT while the DC is Host-Only, Desktop2 cannot reach the domain (ping fails, join fails).
- Fix: put both machines on the same Host-Only/Internal adapter.

---

## 2) Create OUs and a security group for HR
On **Desktop1** (using RSAT → ADUC):
1. I open **Active Directory Users and Computers** (`dsa.msc`).
2. I right-click the domain `practice1.com` → **New → Organizational Unit**
3. I create an OU called: **HR**
4. Inside **HR**, I create a group:
   - Right-click → **New → Group**
   - Name: `HR`
   - Group scope/type: (typically **Global / Security**)

> Note: OUs help me organize objects and apply Group Policy cleanly by department.

---

## 3) Create user “Patty” and put her in the HR group
Still in ADUC:
1. Domain → `Users` (or directly in HR OU, depending on my structure)
2. **New → User**
3. I create: `Patty`
4. I set password (lab example): `Welcome1`
5. I add Patty to the HR group:
   - Open Patty properties → **Member Of** → Add `HR`

**Security note**
- In a real environment I would not use predictable passwords. This is lab-only.

---

## 4) Create an IT OU and move the Helpdesk account into it
1. In ADUC, I create an OU: **IT**
2. I move the `Helpdesk` user into **IT** (drag/drop or right-click → Move)

Purpose:
- I keep IT/admin-type accounts separate from normal user OUs.
- This makes delegation and policy targeting easier later.

---

## 5) View detailed user attributes (advanced)
On Desktop1 (ADUC):
1. **View → Advanced Features** (enable)
2. Open `HR` OU → open Patty → **Attribute Editor**

Purpose:
- This is useful for troubleshooting (timestamps, attributes, account settings).

---

## 6) Understand “which policies apply” (RSOP / Resultant Set of Policy)
### Why I care
Knowing applied policies helps me troubleshoot issues like “why can’t Patty run Task Manager?” or “why did her account lock?”

### RSOP method (user-focused)
From ADUC:
- Find Patty → right-click → **All Tasks**
- Use **Resultant Set of Policy** (logging / planning depending on what I need)

> Logging = shows what actually applied on a machine after login.
> Planning = simulation (what would apply if…).

### GPMC method (policy-focused)
On the server (or via RSAT):
1. Open **Group Policy Management** (`gpmc.msc`)
2. `Forest → Domains → practice1.com`
3. Check:
   - **Group Policy Objects**
   - **Linked Group Policy Objects**
4. Open **Default Domain Policy → Settings → Show All**

Good practice:
- I take a screenshot of key settings for documentation.

---

## 7) Helpdesk security task: set account lockout policy
In **Default Domain Policy**:
1. `GPMC → Default Domain Policy → Edit`
2. `Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy`
3. I set:
   - **Account lockout threshold** = `4`
   - **Account lockout duration** = `30 minutes`
   - **Reset account lockout counter after** = `30 minutes`

Purpose:
- Reduce brute force attempts (lab simulation of common org policy).

---

## 8) Set up Desktop2 network + join the domain
### 8.1 Enable local admin account (only if my edition supports it)
If Desktop2 is Windows Pro/Enterprise I can use:
- `This PC → Manage → Local Users & Groups → Users → Administrator`
- Uncheck “Account is disabled”
- Set password

**Important correction**
- Windows **Home** cannot join a domain and often doesn’t have Local Users & Groups UI.
- Desktop2 must be **Windows Pro** to join `practice1.com`.

### 8.2 Configure Desktop2 IP + DNS (critical)
On Desktop2:
1. `Control Panel → Network and Sharing Center → Change adapter settings`
2. `Ethernet → Properties → IPv4`
3. I set (example):
   - IP: `10.1.10.4`
   - Subnet: `255.255.255.0` (or my lab subnet)
   - DNS: `10.1.10.2` (Domain Controller)

**Important correction**
- Domain join depends on DNS pointing to the DC.
- The “Default Gateway = 10.1.10.1” is only correct if I actually have a router at `10.1.10.1`. Otherwise I leave gateway blank.

### 8.3 Test connectivity
On Desktop2:
- `ping 10.1.10.2`
- `nslookup practice1.com`

### 8.4 Join the domain
On Desktop2:
1. `Win + R → sysdm.cpl`
2. `Computer Name → Change`
3. Select **Domain**: `practice1.com`
4. Enter credentials:
   - `PRACTICE1\Administrator` (or `PRACTICE1\Helpdesk` if allowed)
5. Restart

### 8.5 Verify
On Desktop1 (ADUC):
- Domain → **Computers**
- I confirm `Desktop2` appears.

---

## 9) Login test with Patty
On Desktop2:
1. Log in as:
   - `PRACTICE1\Patty` (or `Patty@practice1.com`)
2. After login, I can review:
   - Patty’s attributes in ADUC (last logon, etc.)
   - Applied policies via RSOP

---

## 10) Useful command reference (Part 5)
- Show domain user info:
  - `net user helpdesk /domain`
- Force Group Policy refresh (on a client):
  - `gpupdate /force`
- Show applied policy summary:
  - `gpresult /r`

---

## Notes / mistakes I hit
- If Desktop2 cannot join the domain, I check **DNS first**, then VirtualBox network mode (NAT vs Host-Only mismatch).
- If “Domain” field is greyed out, Desktop2 is likely Windows Home (must be Pro).
