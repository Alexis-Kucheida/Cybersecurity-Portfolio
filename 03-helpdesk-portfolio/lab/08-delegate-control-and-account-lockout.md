# Runbook — Delegate Control + Account Lockout Investigation (KevTech Part 14)

## Objective
I practice two common helpdesk / junior admin tasks in my `practice1.com` lab:
1) Delegate limited Active Directory permissions to a consultant account (`Scott`) using **Delegate Control**
2) Investigate and resolve an **account lockout** using the Microsoft Account Lockout tools (LockoutStatus)

---

## Lab context (my setup)
- Domain: `practice1.com`
- Domain Controller: `Server2025`
- Admin tools host: `Server2025` (ADUC) and/or `Desktop1` (RSAT)
- Test account: `Scott` (consultant)

---

## Corrections to my notes (important)
- I keep naming consistent: `Server2025` and `practice1.com`.
- Delegate Control is applied on an **OU** (or container), not globally.
- LockoutStatus is a read-only investigation tool; unlocking is performed in ADUC (or via administrative actions).
- Password `Welcome1` is lab-only and not secure for real environments.

---

# Part A — Create a consultant user (Scott)

## A1) Create the user in ADUC
On `Server2025`:
1) Open ADUC:
   - Server Manager → Tools → Active Directory Users and Computers
2) Go to:
   - `practice1.com` → `Users` (or your user OU)
3) Create a new user:
   - Right-click → New → User
   - Name: `Scott`
4) Set password:
   - `Welcome1` (lab)
5) Finish user creation

---

# Part B — Create an OU for consultants and move Scott into it

## B1) Create the OU
On `Server2025` in ADUC:
1) Right-click the domain: `practice1.com`
2) New → Organizational Unit
3) Name: `Consultants`
4) OK

## B2) Move Scott into the Consultants OU
On `Server2025` in ADUC:
1) Find user `Scott`
2) Right-click `Scott` → Move
3) Select OU: `Consultants`
4) OK

Purpose: I isolate consultant accounts into an OU so I can delegate and apply policies cleanly without affecting the rest of the domain.

---

# Part C — Delegate limited control to Scott (reset passwords only)

## C1) Run the Delegation of Control Wizard
On `Server2025` in ADUC:
1) Right-click OU: `Consultants`
2) Select: Delegate Control…
3) Next
4) Add `Scott`
5) Next
6) Select the task:
   - Reset user passwords and force password change at next logon
7) Next → Finish

Goal: Scott can perform only password resets in that OU and nothing else (no disabling accounts, no deleting, no changes beyond delegated task).

---

# Part D — Verify Scott’s delegated permissions

## D1) Log in as Scott on another machine
On another domain-joined VM (example: `Desktop2`):
1) Sign out current user
2) Sign in as:
   - `PRACTICE1\Scott`
3) Open AD tools:
   - Windows Tools / Administrative Tools → Active Directory Users and Computers (if RSAT is installed)

## D2) Confirm restricted access
In ADUC (as Scott):
1) Browse to OU: `Consultants`
2) Try to open user properties

Expected result:
- Most settings are greyed out (no privilege)
- Scott can reset passwords:
  - Right-click a user in the OU → Reset Password (available)
- Actions like Disable Account, Delete, or OU-wide management should be denied/unavailable

---

# Part E — Install LockoutStatus (Account Lockout Tool) on Server2025

## E1) Transfer the tool into the lab via shared folder
On my host PC:
1) Download the Account Lockout tools (LockoutStatus executable)
2) Place the installer/tool files in my shared folder:
   - `C:\Downloads\Alexis_Lab`

In VirtualBox for `Server2025`:
1) Devices → Shared Folders → ensure `Alexis_Lab` is mounted
2) Confirm the folder is accessible inside `Server2025`:
   - `\\VBOXSVR\Alexis_Lab`

## E2) Install / run LockoutStatus on Server2025
On `Server2025`:
1) Open `\\VBOXSVR\Alexis_Lab`
2) Run the LockoutStatus tool (or its installer if provided)
3) Launch LockoutStatus

---

# Part F — Reproduce an account lockout (Scott)

## F1) Lock the account intentionally
On a client VM login screen:
1) Sign out `Scott` if already logged in
2) Enter a wrong password multiple times until the account becomes locked

Note: Lockout threshold and duration depend on the domain lockout policy (Default Domain Policy settings).

---

# Part G — Investigate lockout using LockoutStatus

## G1) Query the user in LockoutStatus
On `Server2025`:
1) Open LockoutStatus
2) File → Select Target
3) Enter: `Scott`
4) OK

What I can observe:
- Which domain controller shows the lockout state
- Lockout timestamps and related attributes
- Useful investigation data that the user cannot reliably describe (how many attempts, when, where it was observed)

---

# Part H — Resolve the lockout (unlock Scott)

## H1) Unlock in ADUC
On `Server2025` in ADUC:
1) Navigate to OU: `Consultants`
2) Open user `Scott` → Properties
3) Tab: Account
4) Check: Unlock account
5) Apply / OK

Alternative:
- Right-click `Scott` and perform admin actions as needed (depending on your ADUC view/options)

---

## Validation checklist (done criteria)
- `Scott` exists in `practice1.com` and is located in OU `Consultants`
- Delegation is applied so Scott can reset passwords in `Consultants` but cannot perform other admin actions
- I can reproduce a lockout for Scott and confirm lockout state in LockoutStatus
- I can unlock Scott in ADUC and Scott can log in again
