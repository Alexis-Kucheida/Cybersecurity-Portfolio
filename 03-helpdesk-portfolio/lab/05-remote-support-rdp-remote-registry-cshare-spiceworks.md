# Runbook — Remote Support + Ticketing Workflow (KevTech Video 8 and 13 : Remote/RDP + Spiceworks)

## Objective
I practice two core helpdesk competencies in my `practice1.com` lab:
1) Remote support of a user workstation using **RDP**, **admin shares (C$)**, and **Remote Registry**  
2) Ticket handling and remote support sessions using **Spiceworks Cloud Help Desk**

---

## Lab context (my setup)
- Domain: `practice1.com`
- Domain Controller: `Server2025`
- Helpdesk workstation: `Desktop1`
- User workstation: `Desktop2`
- Helpdesk account: `PRACTICE1\Helpdesk`
- User account: `PRACTICE1\Patty`

---

# Part 1 — Remote Desktop (RDP) from Desktop1 to Desktop2

## 1.1 Pre-checks (before changing anything)
On `Desktop1`, I confirm `Desktop2` is reachable and resolvable:

    ping Desktop2
    nslookup Desktop2

If hostname resolution is unreliable, I test with Desktop2’s IP:

    ping 10.1.10.X

If ping fails, I stop and fix networking first:
- Desktop1 and Desktop2 must be on the same lab network (not mixed NAT/Host-Only).
- Desktop2 must use DNS = Server2025 IP (domain DNS).
- Desktop2 must be joined to `practice1.com`.

---

## 1.2 Enable Remote Desktop on Desktop2
**Correction:** I do NOT “enter Helpdesk credentials” to enable Remote Desktop. I enable RDP locally on Desktop2, then I authorize Helpdesk to connect.

On `Desktop2`:
1) `Win + R` → `sysdm.cpl`  
2) Remote tab  
3) Enable **Allow remote connections to this computer**  
4) Accept firewall prompts if asked  

---

## 1.3 Allow Helpdesk to connect via RDP
Preferred method (Desktop2 is Windows Pro):
1) On `Desktop2`: `Win + R` → `lusrmgr.msc`  
2) Groups → **Remote Desktop Users**  
3) Add: `PRACTICE1\Helpdesk`

Command-line alternative (lab):
- Add Helpdesk to Remote Desktop Users:

      net localgroup "Remote Desktop Users" PRACTICE1\Helpdesk /add

If I need a quick permissive test (lab only), I can add Helpdesk as local admin on Desktop2:

      net localgroup administrators PRACTICE1\Helpdesk /add

---

## 1.4 Connect from Desktop1 to Desktop2 using RDP
On `Desktop1`:
1) `Win + R` → `mstsc`  
2) Computer: `Desktop2.practice1.com` (or `Desktop2` or Desktop2 IP)  
3) Username: `PRACTICE1\Helpdesk`  
4) Connect

Result: I can access Patty’s workstation to troubleshoot apps, updates, files, and configuration.

---

## 1.5 Troubleshooting RDP failures (step-by-step)

### 1.5.1 Flush DNS cache
**Correction:** Flushing DNS is most useful on the connecting machine (`Desktop1`) first. I can also run it on `Desktop2` if needed.

On `Desktop1` (and optionally `Desktop2`):

    ipconfig /flushdns

### 1.5.2 Fix time drift (common in VM/domain labs)
Problem: connection/authentication fails due to time differences (Kerberos is time-sensitive).

Steps:
1) Correct time/date on `Server2025`  
2) On `Desktop1` and `Desktop2`:

    w32tm /resync

### 1.5.3 “A certification authority could not be contacted for authentication” / NLA issues
When I see this type of authentication error, I troubleshoot in this order:
1) Confirm RDP is enabled on Desktop2 (Section 1.2).  
2) Confirm Windows Firewall allows Remote Desktop (TCP 3389). I avoid permanently disabling the firewall.  
3) Temporary lab test: disable NLA on Desktop2:
   - `sysdm.cpl` → Remote tab  
   - Uncheck the option that requires Network Level Authentication (NLA)  
4) Restart Desktop2 and try RDP again.

---

# Part 2 — Admin share access (C$) and drive mapping visibility

## 2.1 Identify active network connections (mapped drives)
On the machine I’m investigating (Desktop2, via RDP or locally):

    net use

This shows mapped drives and active share connections.

## 2.2 Access Desktop2 files remotely using admin share C$
From `Desktop1` (Helpdesk):
1) Open File Explorer  
2) In the address bar, type:

    \\Desktop2\C$

Result: I can access Desktop2’s C: drive (admin rights required). This is useful for logs, installers, and user files during troubleshooting.

---

# Part 3 — Remote Registry (Desktop1 → Desktop2)

## 3.1 Connect to a remote registry
On `Desktop1`:
1) `Win + R` → `regedit`  
2) File → **Connect Network Registry…**  
3) Target: `Desktop2`

## 3.2 Ensure the required service is running on Desktop2
**Correction:** The relevant service is **Remote Registry** (NOT “Remote Access Connection Management”).

On `Desktop2`:
1) `Win + R` → `services.msc`  
2) Find **Remote Registry**  
3) Set Startup type: **Automatic**  
4) Click **Start**  
5) Retry Remote Registry connection from Desktop1.

Security note: Remote Registry is often disabled in production environments; enable only when necessary.

---

# Part 4 — Windows Remote Assistance (alternate remote help method)

## 4.1 Helpdesk connects using an invitation
On `Desktop2` (Patty):
1) Open **Windows Remote Assistance**  
2) Choose to invite someone to help  
3) Save the invitation file on the Desktop

On `Desktop1` (Helpdesk):
1) Retrieve the invitation via:

    \\Desktop2\C$

2) Open the invitation file to connect.

---

# Part 5 — Ticketing workflow + Remote Support via Spiceworks (Cloud Help Desk)

## 5.1 Create and open Spiceworks Cloud Help Desk
1) I create a Spiceworks account.  
2) I log in and open **Cloud Help Desk**.  
3) I review the two pre-existing tickets included for practice.

## 5.2 My standard ticket workflow
For each ticket, I follow this process:
1) Read the user report and confirm impact.  
2) Triage: category + priority + assign to the right person.  
3) Investigate: collect evidence, run checks, take notes.  
4) Fix: apply the smallest safe change.  
5) Verify: confirm the issue is resolved.  
6) Document: what I did, commands used, outcome.  
7) Close the ticket with a clear resolution summary.

---

## 5.3 Spiceworks Remote Support via session key (lab method)

### 5.3.1 Temporarily give Desktop2 internet access (DHCP)
On `Desktop2`:
1) Control Panel → Network and Sharing Center → Change adapter settings  
2) Ethernet → Properties → IPv4  
3) Switch from static IP to:
   - Obtain an IP address automatically (DHCP)
   - Obtain DNS server address automatically

### 5.3.2 Temporarily change VirtualBox network settings for Desktop2
On the host (VirtualBox settings for `Desktop2`):
1) Network → Adapter 1:
   - Attached to: **Bridged Adapter**
2) Promiscuous Mode:
   - **Allow All** (only if required in my environment)

### 5.3.3 Join the remote support session
On `Desktop2`:
1) Open the join page (example from my notes): `join.zoho.com`  
2) Enter the session key  
3) Download/run the remote support app if prompted  
4) Confirm **Join session**

Result: I can control Patty’s Desktop2 remotely and use tools like:
- Invite another technician
- Elevate privileges (if supported) to investigate
- Take screenshots and capture evidence for the ticket

### 5.3.4 End session and restore lab settings (critical)
After the remote session:
1) End the session.  
2) Restore `Desktop2` network back to lab configuration:
   - Static IP (my lab IP plan)
   - DNS back to Server2025 IP
3) Restore VirtualBox adapter back to my lab mode (Host-Only/Internal).
4) Validate domain connectivity again on Desktop2:

    ping Server2025

---

## Validation checklist (done criteria)
- RDP works from Desktop1 → Desktop2 (`mstsc`)
- I can access Desktop2 admin share: `\\Desktop2\C$`
- Remote Registry connection works (Remote Registry service started on Desktop2)
- I can create/assign/document/close tickets in Spiceworks
- I can run a remote session via session key and then restore Desktop2 to the lab network without breaking domain functionality
