# Runbook 03 — Common Active Directory Issues + Useful Commands (KevTech Part 6)
**Goal:** I practice and document common Active Directory helpdesk issues (locked/disabled/expired user accounts, workstation trust problems, and “PC offline” type situations). I also capture the commands I use to verify and document results.

---

## Prerequisites
- Domain: `practice1.com`
- DC: Server 2025 (Domain Controller)
- Users/OU: `HR` with user `Patty`
- Workstations: `Desktop1` (admin/RSAT) and `Desktop2` (domain-joined)

---

## 1) Issue: User account locked out

### 1.1 How I reproduce the issue (lab)
1. On Desktop2, I log out.
2. I attempt to log in as `PRACTICE1\Patty` with the wrong password repeatedly until Windows shows the account is locked.

> Note: The lockout threshold/duration comes from the domain account lockout policy (often set in Default Domain Policy).

### 1.2 How I fix it
On the Domain Controller (or Desktop1 via RSAT):
1. Open **Active Directory Users and Computers** (`dsa.msc`)
2. Navigate: `practice1.com → HR → Patty`
3. Right-click `Patty` → **Properties**
4. Tab **Account**
5. Check **Unlock account** → Apply

### 1.3 How I verify
- Patty can log in again successfully on Desktop2.

---

## 2) Issue: User account disabled

### 2.1 How I reproduce the issue (lab)
1. In ADUC, I disable Patty:
   - Right-click `Patty` → **Disable Account**

### 2.2 How I fix it
1. In ADUC:
   - Right-click `Patty` → **Enable Account**
2. Optional helpdesk action:
   - Right-click `Patty` → **Reset Password**
   - Check **User must change password at next logon** (good practice in real environments)

### 2.3 How I verify
- Patty can log in again (or is forced to change password at next logon if enabled).

---

## 3) Issue: User account expired

### 3.1 How I reproduce the issue (lab)
1. In ADUC:
   - `Patty → Properties → Account`
2. Set **Account expires** to a date in the past.

### 3.2 How I fix it
1. In ADUC:
   - `Patty → Properties → Account`
2. Set **Account never expires** (or set a valid future expiration date)

### 3.3 How I verify (command line)
On a domain-joined machine:
```powershell
net user patty /domain
