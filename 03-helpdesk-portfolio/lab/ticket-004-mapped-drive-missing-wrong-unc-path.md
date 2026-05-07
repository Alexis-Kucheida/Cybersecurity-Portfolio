# Ticket 004 — Mapped Drive Missing / Cannot Access Share (Wrong UNC Path)

## Ticket metadata
- **Date:** 28/02/2026
- **Category:** Windows / File Shares / Active Directory Groups  
- **Priority:** Medium  
- **Environment:** VirtualBox lab (Server 2025 file server + Desktop2 client)  
- **Domain:** practice1.com  
- **Affected user:** Patty (HR)  
- **Affected workstation:** Desktop2  
- **Share(s):** HR, Personal  

---

## User reported issue
Patty reports that the HR share and/or mapped drive does not appear in File Explorer (or access fails). Example symptoms:
- Mapped drive `Z:` is missing after sign-in
- `\\ServerXXXX\HR` returns “network path not found”
- HR share does not show under Network

---

## Impact
User cannot access HR documents or personal drive from Desktop2.

---

## Initial triage
1. Confirm the exact path being used (UNC):
   - `\\Server2025\HR` (example)
2. Confirm whether the issue is:
   - **Name/path issue** (wrong server name or typo)
   - **Network issue** (client can’t reach server)
   - **Permissions issue** (user can reach share but access denied)

---

## Investigation

### 1) Verify basic connectivity (client-side)
On Desktop2 (Patty or local test):
```cmd
ping 10.1.10.2
