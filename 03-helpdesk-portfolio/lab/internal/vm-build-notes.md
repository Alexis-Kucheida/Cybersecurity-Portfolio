# VM Build Notes (VirtualBox)

## Purpose
This lab supports Helpdesk practice (Windows troubleshooting + basic AD/domain workflows later).

## Host Environment
- Host OS: Windows (host machine)
- Hypervisor: Oracle VirtualBox
- Notes: Documented after initial setup (retroactive)

## Virtual Machines
### VM 1 — Windows 11 (client)
**Name (VirtualBox):** Windows 11 (Instantané 1)  
**State:** Powered off (at time of documentation)

**OS**
- Windows 11 (64-bit)

**Compute**
- RAM: 8192 MB
- vCPU: 2
- Boot order: Hard Disk, Optical, Floppy

**Security / Firmware**
- TPM: 2.0
- EFI: Enabled
- Secure Boot: Enabled

**Display**
- Video memory: 128 MB
- Graphics controller: VMSVGA
- Remote display server: Disabled
- Recording: Disabled

**Storage**
- Controller: SATA
- Disk: `Windows 11.vdi` (Normal, 80.00 GB)
- Optical: `VBoxGuestAdditions.iso` mounted

**Network**
- Adapter 1: Intel PRO/1000 MT Desktop
- Mode: NAT

### VM 2 — Kali Linux (optional / security tooling)
**Name (VirtualBox):** `kali-linux-2025.4-virtualbox-amd64`  
**State:** Saved

## Snapshots
- Windows 11: “Instantané 1” exists
- Recommendation: maintain snapshots at key milestones:
  - Baseline OS installed
  - After domain join (future)
  - After GPO testing (future)


