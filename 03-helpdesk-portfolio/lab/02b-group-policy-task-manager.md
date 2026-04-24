# Runbook 02b — Disable Task Manager via GPO + Verify with RSOP (KevTech Part 9)
**Goal:** I create a GPO that disables Task Manager for a specific user (Patty), link it to the HR OU, enforce it, and verify it using `gpupdate` and `gpresult`.

---

## 1) Create a new GPO
On the server (or RSAT):
1. Open **Group Policy Management** (`gpmc.msc`)
2. `practice1.com → Group Policy Objects`
3. Right-click → **New**
4. Name: `Disable Task Manager`

---

## 2) Edit the GPO (important: use USER configuration)

1. Right-click GPO → **Edit**
2. Go to:
   - `User Configuration → Policies → Administrative Templates → System → Ctrl+Alt+Del Options`
3. Set:
   - **Remove Task Manager** = `Enabled`

---

## 3) Link the GPO to the HR OU and enforce
1. In GPMC, right-click the **HR OU** → **Link an Existing GPO**
2. Select: `Disable Task Manager`
3. (Optional) right-click the linked GPO → **Enforced**

> Linking at the OU level ensures it targets HR users (including Patty) without affecting IT/admin OUs.

---

## 4) Update policy on Desktop2 (as Patty)
On Desktop2, logged in as Patty:
1. Open CMD:
   - `gpupdate /force`
2. Test:
   - Ctrl+Shift+Esc (Task Manager) should be blocked
   - `taskmgr` should be blocked for the user

---

## 5) Verify with gpresult and RSOP
On Desktop2 (Patty):
- `gpresult /r` (shows applied GPOs)

From GPMC:
- `Group Policy Results → Wizard` (generate a report for Patty + Desktop2)

From ADUC:
- Patty → **All Tasks → Resultant Set of Policy**
