# Rebuild Checklist (VirtualBox Lab)

## Baseline (Client VM)
1. Create Windows 11 VM (64-bit)
2. Assign resources (RAM/CPU/disk)
3. Enable EFI + Secure Boot + TPM 2.0 (for Windows 11 requirements)
4. Set networking to NAT
5. Install Windows 11
6. Install VirtualBox Guest Additions
7. Create a baseline snapshot (e.g., "BASELINE-OS")

## Next (Planned Domain Lab)
8. Create Windows Server VM
9. Configure lab network (Host-Only or Internal Network recommended)
10. Install AD DS + DNS
11. Promote to Domain Controller
12. Create test OU structure + users + groups
13. Join Windows 11 to domain
14. Validate login with domain user
15. Create and test a GPO
16. Create a file share + group-based access
17. Generate 1–2 “tickets” by breaking DNS/permissions and documenting the fix
18. Snapshot after each major milestone

