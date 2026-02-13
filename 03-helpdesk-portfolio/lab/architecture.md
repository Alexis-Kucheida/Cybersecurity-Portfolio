# Lab Architecture

## Goal
Home lab designed for entry-level Helpdesk practice:
- Windows troubleshooting and administration basics
- Domain concepts preparation (AD/GPO later)
- Repeatable ticket-style scenarios for a GitHub portfolio

## Current State (as of documentation)
- VirtualBox installed
- Windows 11 VM created and configured (client workstation)
- Optional Kali VM available (not required for Helpdesk)

## Components
- Hypervisor: Oracle VirtualBox
- Client VM: Windows 11 (64-bit)
- Optional VM: Kali Linux (saved state)

## Networking
- Windows 11 VM network mode: NAT (VirtualBox)
  - Intended use: basic connectivity and updates without exposing VM directly on LAN
  - Note: when adding a domain controller later, consider Host-Only or an Internal Network so client + server can communicate in an isolated lab network.

## What this lab will support next
Planned additions (Helpdesk-focused):
1. Windows Server VM as Domain Controller (AD DS + DNS)
2. Join Windows 11 client to domain
3. Create users/groups, test permissions, troubleshoot common issues
4. GPO creation/testing + RSOP/GPResult verification
5. File shares (Share vs NTFS permissions) + mapped drives

## Outputs for GitHub
- Ticket writeups (domain join failure, lockouts, mapped drive missing, no access to share)
- Runbooks (onboarding/offboarding, password reset, file share provisioning)
- Scripts (PowerShell triage and basic account operations)

