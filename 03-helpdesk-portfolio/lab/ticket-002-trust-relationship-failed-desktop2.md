# Ticket 002 — Trust Relationship Failed (Desktop2 / Patty)

## Ticket metadata
- **Date:** 24/02/206  
- **Category:** Active Directory / Windows Client / Authentication  
- **Priority:** High (user cannot log in)  
- **Environment:** VirtualBox lab (Server 2025 DC + Desktop1 + Desktop2)  
- **Domain:** practice1.com  
- **Affected user:** Patty (HR)  
- **Affected workstation:** Desktop2  

---

## User reported issue
On Desktop2, Patty cannot log in. Windows shows:
> **“The trust relationship between this workstation and the primary domain failed.”**

---

## Impact
- Domain authentication fails on Desktop2.
- Patty cannot access domain resources from that workstation.
- Lab workflow blocked for HR user testing.

---

## Initial assessment (what this error usually means)
This error indicates the **computer account secure channel** between Desktop2 and the domain is broken (machine password mismatch).  
Common lab causes include VM snapshot/restore, cloning, or reverting machine state.

---

## Troubleshooting (steps performed)

### 1) Confirm the account exists in AD
On the Domain Controller:
1. Open **Active Directory Users and Computers** (`dsa.msc`)
2. Navigate to **Computers** (or the OU where workstations are stored)
3. Locate **Desktop2**

Result:
- Desktop2 computer object exists.

### 2) Reset the Desktop2 computer account in Active Directory
On the Domain Controller:
1. In ADUC, right-click **Desktop2**
2. Select **Reset Account**
3. Confirm the reset

Purpose:
- This resets the computer account in AD so Desktop2 can re-establish trust when rejoined/repaired.

### 3) Regain local access on Desktop2
Because domain login fails, I used a **local** account to log into Desktop2:
- Tried `.\Administrator` (local admin) or another known local admin account.

Result:
- Logged in locally (domain login not required).

> Note: If no local admin login is possible, I would use Windows Recovery (ISO) to enable/reset a local admin account, then continue.

### 4) Remove Desktop2 from the domain (switch to workgroup)
On Desktop2 (local admin session):
1. `Win + R` → `sysdm.cpl`
2. Tab **Computer Name** → **Change**
3. Select **Workgroup** and enter `WORKGROUP`
4. Confirm and restart

Purpose:
- Clears the broken domain trust association.

### 5) Join Desktop2 back to the domain
After reboot (still local admin):
1. `Win + R` → `sysdm.cpl`
2. **Computer Name** → **Change**
3. Select **Domain**: `practice1.com`
4. Enter domain join credentials:
   - `PRACTICE1\Administrator`
5. Restart

### 6) Verify resolution
1. On Desktop2, logged in as:
   - `PRACTICE1\Patty`
2. On the DC (ADUC), verified Desktop2 is present under **Computers** and looks healthy.

Result:
- Patty login succeeds.
- Trust relationship error no longer appears.

---

## Root cause
Desktop2’s secure channel with the domain became invalid (computer account password mismatch), likely due to VM state changes (snapshot/restore/clone behavior in a lab environment).

---

## Resolution
Reset Desktop2 computer account in ADUC and rejoined Desktop2 to the domain (workgroup → domain), restoring a valid secure channel.

---

## Preventive notes
- Avoid frequent snapshot/restore on domain-joined machines unless necessary.
- If using snapshots, prefer taking them **before** joining the domain and avoid reverting domain-joined machines repeatedly.
- Ensure time sync is reasonable between DC and clients (large drift can contribute to auth issues).

---

## References
- Runbook: [02-group-policy-and-rsop.md](./02-group-policy-and-rsop.md)
- Lab baseline: [01-initial-setup-domain-lab.md](./01-initial-setup-domain-lab.md)
