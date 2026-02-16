# Ticket 001 — Unable to Join Domain (Credentials Rejected)

## Ticket metadata
- **Date:** 16/02/2026 
- **Category:** Windows / Active Directory / Networking  
- **Priority:** Medium  
- **Environment:** VirtualBox lab (Windows Server 2025 DC + Windows 11 Pro client)  
- **Domain:** practice1.com  
- **Client hostname:** Desktop1  
- **DC hostname/IP:** Server2025 / 10.1.10.2  

---

## User reported issue
When I try to join the Windows 11 computer to the domain `practice1.com`, Windows prompts for credentials but rejects the Administrator credentials and the domain join fails.

---

## Impact
- The Windows 11 workstation cannot join the domain.
- Domain-based authentication and administration workflows cannot be tested from the client (lab blocked).

---

## Initial observations
- The Domain Controller is promoted and functional (`$env:USERDNSDOMAIN` on the server returns `PRACTICE1.COM`).
- The failure happens at the credential prompt during domain join.

---

## Troubleshooting (what I checked)

### 1) Verify client network configuration
On Windows 11, I checked:
- `ipconfig /all`

I specifically confirmed:
- The client IP is in the same subnet as the DC (lab network).
- The **DNS server** is set correctly.

### 2) Verify connectivity to the Domain Controller
On Windows 11:
- `ping 10.1.10.2`

Result:
- Ping initially failed / timed out.

### 3) Verify name resolution (DNS)
On Windows 11:
- `nslookup practice1.com`
- `nslookup _ldap._tcp.dc._msdcs.practice1.com`

Result:
- Name resolution failed or returned an unexpected DNS server.

### 4) Verify VirtualBox network mode consistency
I checked both VMs network settings in VirtualBox:
- Windows 11 was on **NAT** while Server 2025 was on **Host-Only** (or vice versa).

This explained:
- No direct VM-to-VM connectivity
- DNS queries not reaching the Domain Controller DNS service

---

## Root cause
The Windows 11 VM was not correctly configured to reach the domain controller because:
1) The two VMs were not on the same VirtualBox network (NAT vs Host-Only mismatch), and/or  
2) The Windows 11 client was not using the Domain Controller IP as its DNS server.

This caused the client to fail AD service discovery (DNS SRV lookups), and the domain join failed even with correct credentials.

---

## Resolution (what I changed)

### 1) Align VirtualBox networking
- I set both VMs to the same **Host-Only Adapter** (lab network) so they could communicate directly.

### 2) Correct Windows 11 DNS configuration
On Windows 11 (IPv4 properties):
- **Preferred DNS:** `10.1.10.2` (Domain Controller)
- (Gateway left blank unless a real router exists on that lab network)

### 3) Retry domain join using correct credential format
I joined `practice1.com` again and entered credentials using one of these formats:
- `PRACTICE1\Administrator`
- `Administrator@practice1.com`

---

## Verification (proof it worked)
After reboot:
- On the Server (ADUC): `practice1.com → Computers`
- The client computer account **Desktop1** appeared in the Computers container.

Additional verification on Windows 11:
- `nslookup practice1.com` returns the DC/DNS server
- Successful domain login using domain credentials (optional)

---

## Preventive notes / lessons learned
- Domain join depends on **DNS**, not default gateway. The client must use the DC as DNS to locate AD services.
- In VirtualBox labs, keep domain traffic on a dedicated network (Host-Only or Internal Network).
- When prompted for credentials, use the domain format (`DOMAIN\user` or `user@domain`), not a local account format (`.\user`).

---

## References
- Runbook: `03-helpdesk-portfolio/lab/01-initial-setup-domain-lab.md`
