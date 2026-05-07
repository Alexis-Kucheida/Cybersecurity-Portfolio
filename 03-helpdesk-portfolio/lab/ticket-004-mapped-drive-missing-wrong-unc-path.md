# Ticket 004 — Mapped Drive Missing / Cannot Access HR Share (Wrong UNC Path)

## Ticket metadata
- Date: 28/02/2026
- Type: Incident
- Category: Windows / File Shares / Active Directory
- Priority: Medium
- Environment: VirtualBox lab (Server2025 + Desktop2)
- Domain: practice1.com
- User: Patty (HR)
- Workstation: Desktop2
- Expected drive: Z: -> \\Server2025\HR

## User reported issue
Patty reports the HR drive is missing and she cannot access the HR shared folder. When she tries to open it manually, she gets “Network path not found”.

## Impact
User cannot access HR documents from Desktop2.

## Triage
1) Confirm the UNC path Patty is using (example: \\ServerXXXX\HR)
2) Confirm whether the issue is only HR or also Personal share
3) Confirm if any changes happened recently (server rename, VM restore/snapshot, network adapter change)

## Investigation
### 1) Check connectivity to the file server
Ran on Desktop2:
- ping 10.1.10.2
Result: Success

### 2) Check DNS resolution for the file server hostname
Ran on Desktop2:
- nslookup Server2025
Result: Success

### 3) Validate the UNC path Patty is using
Observed UNC path in mapping: \\Server2026\HR
Tested on Desktop2:
- dir \\Server2026\HR
Result: Network path not found

### 4) Confirm correct server hostname and shares on the server
Confirmed hostname on server:
- hostname
Result: Server2025

Confirmed shares exist (Server Manager -> File and Storage Services -> Shares):
- HR
- Personal

### 5) Confirm the correct UNC path works
Tested on Desktop2:
- dir \\Server2025\HR
Result: Success

## Root cause
Mapped drive was configured with an incorrect UNC path (wrong server hostname: \\Server2026\HR instead of \\Server2025\HR). Because the hostname was wrong, Windows could not locate the share and the drive mapping failed.

## Resolution
### 1) Remove incorrect mapping
On Desktop2:
- net use Z: /delete

### 2) Re-map using correct UNC path
On Desktop2:
- net use Z: \\Server2025\HR /persistent:yes

## Verification
- \\Server2025\HR opens from Desktop2
- Z: is visible in File Explorer after sign-in and points to \\Server2025\HR
- Patty can access files based on HR group permissions

## Preventive actions
- Standardize server naming and reuse the same hostname everywhere.
- Store the correct UNC path in the AD group description (e.g., HR group description = \\Server2025\HR).
- Troubleshooting order for “missing share”:
  1) UNC spelling/hostname
  2) DNS (nslookup)
  3) Connectivity (ping)
  4) Share exists on server
  5) Permissions (Share + NTFS)

## References
- Runbook: [04-file-shares-permissions-mapped-drives-and-printing.md](./04-file-shares-permissions-mapped-drives-and-printing.md)
