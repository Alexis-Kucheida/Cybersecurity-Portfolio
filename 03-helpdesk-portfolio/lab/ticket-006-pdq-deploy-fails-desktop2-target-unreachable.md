# Ticket 006 — PDQ Deploy Package Fails on Desktop2 (Target Unreachable / Access Denied)

## Ticket metadata
- Date: 05/03/2026
- Type: Incident
- Category: Software Deployment / PDQ Deploy / Windows Administration
- Priority: Medium
- Environment: VirtualBox lab (practice1.com)
- Domain: practice1.com
- Server/PDQ host: Server2025
- Technician workstation: Desktop1
- Affected workstation: Desktop2
- Tools: PDQ Deploy, PDQ Inventory
- Account used: PRACTICE1\Administrator (or PRACTICE1\Helpdesk if delegated)

---

## User reported issue
Software deployment from PDQ Deploy fails when targeting Desktop2. The package does not install and the deployment reports an error such as “Target unreachable” or “Access denied”.

---

## Impact
Helpdesk cannot deploy software remotely to Desktop2, delaying standard workstation setup and remediation.

---

## Initial triage
I confirmed:
- Desktop2 is powered on
- Desktop2 is on the same lab network as Server2025
- Desktop2 is domain-joined to practice1.com
- I am running PDQ Deploy from Server2025 with an account that should have admin rights on Desktop2

---

## Investigation

### 1) Verify network connectivity to Desktop2
From Server2025:
    ping Desktop2

Result:
- If ping fails, network configuration or firewall is likely blocking connectivity.

### 2) Verify name resolution (DNS)
From Server2025:
    nslookup Desktop2

Result:
- If DNS fails, I verify Desktop2 is using domain DNS and that Server2025 is the DNS server.

### 3) Verify admin share access (SMB) to Desktop2
From Server2025:
    dir \\Desktop2\C$

Result:
- If this fails, PDQ will usually fail as well (PDQ relies on admin shares and remote services).

### 4) Verify required Windows services and firewall rules (Desktop2)
On Desktop2 (local or via RDP):
- File and Printer Sharing enabled
- Windows Firewall allows SMB (445) and required remote management rules (lab)
- Admin shares are enabled (C$ exists)
- Remote administration is not blocked by policy

### 5) Check credentials used by PDQ
I confirmed the deployment credentials are correct and have local admin access on Desktop2:
- Domain Admin (lab) or a delegated admin account
- If using Helpdesk, ensure Helpdesk is local admin or has required rights

---

## Root cause
Desktop2 was not reachable for PDQ deployment due to one of the following (common in labs):
- Desktop2 was on the wrong VirtualBox network (NAT vs Host-Only/Internal mismatch)
- SMB/admin share access to \\Desktop2\C$ failed (permissions/firewall/services)
- PDQ was using credentials that do not have local admin rights on Desktop2

---

## Resolution

### 1) Restore correct lab networking
On the host (VirtualBox):
- Ensure Server2025 and Desktop2 are on the same lab adapter (Host-Only/Internal)

On Desktop2:
- Restore static IP (lab plan) and DNS pointing to Server2025

### 2) Restore SMB/admin share reachability
On Desktop2:
- Ensure File and Printer Sharing is enabled
- Ensure firewall allows required traffic (lab)
- Confirm admin share works from Server2025:
    dir \\Desktop2\C$

### 3) Confirm PDQ credentials have admin rights
- Use PRACTICE1\Administrator for lab testing, or
- Ensure PRACTICE1\Helpdesk is local admin on Desktop2 if using Helpdesk for deployments

### 4) Retry deployment
On Server2025 in PDQ Deploy:
- Deploy package (Zoom Client) to Desktop2 again
- Confirm deployment completes successfully

---

## Verification
- PDQ Deploy shows successful deployment status for Desktop2
- Zoom (or test package) appears in Apps/Programs on Desktop2
- Optional: PDQ Inventory reflects the newly installed application on Desktop2 after next scan

---

## Preventive actions
- Keep all lab targets on the same VirtualBox network during deployment/inventory runs
- Use consistent DNS (domain DNS on Server2025) and avoid leaving clients on DHCP/Bridged after internet tasks
- Validate \\Desktop2\C$ access from Server2025 before attempting deployments

---

## References
- Runbook: 07-pdq-deploy-and-inventory-software-deployment.md
