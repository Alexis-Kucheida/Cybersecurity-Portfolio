# Ticket 005 — RDP Connection Fails from Desktop1 to Desktop2 (Time Drift / NLA)

## Ticket metadata
- Date: 02/03/2026
- Type: Incident
- Category: Remote Support / RDP / Active Directory
- Priority: High
- Environment: VirtualBox lab (practice1.com)
- Domain: practice1.com
- Server/DC: Server2025
- Technician workstation: Desktop1 (Helpdesk)
- Affected workstation: Desktop2 (Patty)
- Accounts:
  - PRACTICE1\Helpdesk
  - PRACTICE1\Patty

---

## User reported issue
Helpdesk cannot connect to Desktop2 via Remote Desktop (RDP). Connection attempts fail when using `Desktop2.practice1.com`.

---

## Impact
Remote support cannot be provided to Patty on Desktop2. Troubleshooting and remediation are blocked.

---

## Initial triage
I confirmed:
- Desktop2 is powered on and reachable on the lab network
- Desktop1 and Desktop2 are on the same VirtualBox network segment (Host-Only/Internal)
- Desktop2 is joined to practice1.com

---

## Investigation

### 1) Verify basic connectivity and name resolution
On Desktop1:
    ping Desktop2
    nslookup Desktop2

Result:
- Ping succeeds (network connectivity OK)
- DNS resolves Desktop2 to the expected IP

### 2) Confirm Remote Desktop is enabled on Desktop2
On Desktop2 (local console if available):
- `sysdm.cpl` → Remote tab → “Allow remote connections to this computer”

Result:
- Remote Desktop enabled

### 3) Check if Helpdesk is allowed to RDP
On Desktop2:
- Local Group “Remote Desktop Users” includes `PRACTICE1\Helpdesk`  
(Or Helpdesk is local admin for lab testing.)

Result:
- Permission is correct

### 4) Identify likely authentication causes
Observed symptom: RDP authentication fails intermittently. In a domain lab, the two most common causes are:
- Time drift between DC and clients (Kerberos)
- NLA/certificate-related authentication issues

---

## Root cause
Desktop1/Desktop2 had time drift relative to Server2025, which caused domain authentication failures when initiating RDP. In some attempts, NLA-related authentication errors also contributed.

---

## Resolution

### 1) Correct time on the Domain Controller
On Server2025:
- Correct system date/time (manual or time source correction)

### 2) Resync time on Desktop1 and Desktop2
On Desktop1 and Desktop2 (run as admin):
    w32tm /resync

### 3) If NLA error persists (lab troubleshooting)
On Desktop2:
- `sysdm.cpl` → Remote tab → temporarily disable “Allow connections only from computers running Network Level Authentication (NLA)”
- Restart Desktop2
- Retry RDP

(Note: In production, keep NLA enabled; this step is for lab isolation only.)

---

## Verification

### 1) Successful RDP connection
From Desktop1:
- `mstsc` → connect to `Desktop2.practice1.com` using `PRACTICE1\Helpdesk`
Result: RDP session established

### 2) Confirm remote support capability
- Helpdesk can access Desktop2 and perform support actions
- Optional: verify admin share if needed:
  - `\\Desktop2\C$`

---

## Preventive actions
- Ensure Server2025 is the authoritative time source for the lab.
- Avoid VM snapshots/restores that introduce time drift without resync.
- Include time check as an early step in any domain authentication troubleshooting.

---

## References
- Runbook: `05-remote-support-and-ticketing-spiceworks.md`
