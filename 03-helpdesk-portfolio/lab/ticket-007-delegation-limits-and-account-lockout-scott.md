# Ticket 007 — Consultant Cannot Manage AD User (Delegation Too Restrictive) + Account Locked Out (Scott)

## Ticket metadata
- Date: 10/03/2026
- Type: Incident
- Category: Active Directory / Delegation / Account Lockout
- Priority: Medium
- Environment: VirtualBox lab (practice1.com)
- Domain: practice1.com
- Domain Controller: Server2025
- Affected user: Scott (Consultant)
- Affected OU: Consultants

---

## User reported issue
Scott (consultant) reports two issues:
1) He cannot perform expected actions in Active Directory (most options are greyed out / access denied).
2) He is locked out after multiple failed sign-in attempts.

---

## Impact
- Consultant cannot complete delegated helpdesk task(s) for accounts in the Consultants OU.
- Consultant cannot log in to the domain to work.

---

## Initial triage
I confirmed:
- Scott exists in Active Directory and is located in the `Consultants` OU
- Scott is not a Domain Admin (expected)
- The delegation model is intended to restrict Scott’s rights

---

## Investigation

### 1) Verify OU placement and delegation scope
On Server2025 (ADUC):
- `practice1.com` → `Consultants` OU
- Confirm Scott is in the correct OU
- Confirm Delegation of Control was applied on the `Consultants` OU (not elsewhere)

### 2) Verify Scott’s effective permissions
On a domain-joined client (logged in as Scott), I opened ADUC and verified:
- User properties are mostly read-only/greyed out
- Scott can access password reset functions on users within the delegated scope (if correctly delegated)

Observed:
- Scott can reset passwords but cannot disable accounts or perform other admin actions (expected if delegation was password-reset only).

### 3) Check lockout status (why Scott cannot log in)
On Server2025:
- Used LockoutStatus to query Scott:
  - File → Select Target → Scott

Observed:
- Scott is locked out on the domain (lockout flags and timestamps present).

---

## Root cause
1) Delegation was intentionally configured to allow **only** password resets (consultant limited access). Therefore, most ADUC options are unavailable to Scott by design.
2) Scott’s account became locked due to repeated failed sign-in attempts exceeding the domain lockout threshold.

---

## Resolution

### 1) Confirm or adjust delegated permissions (if required)
If Scott needs ONLY password-reset ability:
- No change required.

If Scott needs additional rights (example: enable/disable accounts in Consultants OU):
- Re-run Delegation of Control on the `Consultants` OU and add the specific additional tasks required (do not over-privilege).

### 2) Unlock Scott’s account
On Server2025 (ADUC):
1) `practice1.com` → `Consultants` → Scott
2) Properties → Account tab
3) Check “Unlock account”
4) Apply

---

## Verification
- Scott can log in successfully after unlock.
- Logged in as Scott, ADUC shows:
  - Password reset actions available (and any additional delegated tasks if added).
  - Unrelated admin actions remain unavailable (least privilege preserved).

---

## Preventive actions
- Document exactly what consultants are allowed to do (least privilege).
- If lockouts happen frequently, ensure users know correct credentials and provide a standard password reset process.
- Use LockoutStatus to confirm domain-wide lockout state before making changes.

---

## References
- Runbook: 07-delegate-control-and-account-lockout.md
