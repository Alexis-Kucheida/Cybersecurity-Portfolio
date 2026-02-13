# RB-XXX — <Runbook title>
**Owner:** <you>  
**Created:** YYYY-MM-DD  
**Last updated:** YYYY-MM-DD  
**Scope:** Helpdesk / Desktop Support / Junior SysAdmin  

## 1) Objective
- <1 sentence describing what this runbook accomplishes>

## 2) Preconditions
- Required access/role: <Domain Admin / Local Admin / Standard user>
- Systems required: <DC01, FILE01, WIN11-CLIENT01>
- Tools required: <RSAT / Server Manager / MMC snap-ins>
- Network requirements: <client can resolve DC DNS, etc.>

## 3) Inputs (What you need)
- Username:
- Device name:
- Group(s) to assign:
- Share path / printer name:
- Ticket number (if applicable):

## 4) Procedure (Step-by-step)
Write steps so someone can follow without the video.
1. <step>
2. <step>
3. <step>
4. <step>

## 5) Validation (How to confirm success)
- Command checks:
  - `gpupdate /force` (if relevant)
  - `gpresult /r` (if relevant)
  - `whoami /groups` (if relevant)
- Functional checks:
  - <user can log in>
  - <mapped drive appears and is accessible>
  - <printer test page prints>

## 6) Rollback (How to undo safely)
- <rollback steps>
- Notes: <what to avoid>

## 7) Common Issues & Fixes
- **Issue:** <symptom>  
  **Likely cause:** <cause>  
  **Fix:** <steps>

- **Issue:** <symptom>  
  **Likely cause:** <cause>  
  **Fix:** <steps>

## 8) Security Notes (Least privilege / audit / risk)
- <what permissions to avoid granting>
- <what should be logged or reviewed>
- <what should be escalated>

## 9) Documentation / Outputs
- Update ticket with:
  - actions taken
  - validation performed
  - user confirmation (if applicable)
- Artifacts created:
  - <GPO name>
  - <AD group name>
  - <Share name/path>

## 10) References
- Video/Resource: <title> (timestamp range)
- Internal notes: <links to related tickets/runbooks>
