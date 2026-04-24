# Ticket 003 — User Cannot Log In (Locked / Disabled / Expired Account)

## Ticket metadata
- **Date:** 26/02/2026  
- **Category:** Active Directory / Authentication  
- **Priority:** High (user cannot log in)  
- **Environment:** VirtualBox lab (Server 2025 DC + Desktop2 workstation)  
- **Domain:** practice1.com  
- **Affected user:** Patty (HR)  
- **Affected workstation:** Desktop2  

---

## User reported issue
User Patty reports that she cannot log in to Desktop2 with her domain account.

---

## Impact
- User cannot access domain resources.
- Business workflow blocked for HR user.

---

## Initial triage
1. Confirm exact error at login (examples):
   - “The referenced account is currently locked out…”
   - “Your account has been disabled…”
   - “Your account has expired…”
2. Confirm username format used:
   - `PRACTICE1\Patty` or `Patty@practice1.com`

---

## Investigation (performed on Domain Controller)
### 1) Check account status in ADUC
1. Open **Active Directory Users and Computers** (`dsa.msc`)
2. Navigate to: `practice1.com → HR → Patty`
3. Open **Properties → Account**

Checked:
- Account lockout status
- Enabled/disabled status
- Account expiration date

### 2) Command-line verification (optional)
On a domain-joined admin machine:
```cmd
net user patty /domain
