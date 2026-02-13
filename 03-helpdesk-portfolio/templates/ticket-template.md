# TKT-XXX — <Short user-facing issue title>
**Type:** Incident / Service Request  
**Priority:** Low / Medium / High  
**Status:** Open / In Progress / Resolved  
**Created:** YYYY-MM-DD  
**Last updated:** YYYY-MM-DD  

## 1) Summary
**User reported:**  
- <1–2 sentences in plain language>

**Impact:**  
- Who is affected: <single user / team / all users>  
- Business impact: <can’t access share / can’t log in / can’t print / productivity degraded>

## 2) Environment
- Lab/Org: <Home Lab / Practice>  
- Device(s): <WIN11-CLIENT01, etc.>  
- User/account: <test.user1>  
- Domain: <LAB.LOCAL> (if applicable)  
- Network: <NAT / Host-only / Internal>  
- Related services: <AD DS / DNS / DHCP / File Server / Print Server>  

## 3) Symptoms (Observed)
- <bullet>
- <bullet>

## 4) Reproduction (if applicable)
1. <step>
2. <step>
3. <result>

## 5) Initial Triage Checklist
Record what you checked first and why.
- [ ] Confirm scope (one user vs many)
- [ ] Confirm recent changes (updates, password change, GPO changes)
- [ ] Basic connectivity (ping gateway/server)
- [ ] Name resolution (nslookup)
- [ ] Authentication context (local vs domain, group membership)
- [ ] Service availability (server reachable, relevant service running)
- [ ] Check obvious errors (Event Viewer / logs)

## 6) Evidence
Include only what supports your conclusion. Redact sensitive data.
- Screenshot: `<path>`
- Command output:
  - `ipconfig /all` (relevant lines)
  - `whoami /groups` (relevant lines)
  - `gpresult /r` or RSOP summary (relevant lines)
- Logs:
  - Event Viewer: <log name>, Event ID <id>, timestamp

## 7) Findings
What you discovered during triage and investigation.
- <finding 1>
- <finding 2>

## 8) Root Cause (1–2 lines)
- <root cause stated clearly>

## 9) Resolution (Fix)
Document the exact steps taken.
1. <step>
2. <step>
3. <step>

## 10) Validation (Proof it worked)
- What you tested: <login / access share / printer test page / gpupdate>
- Result: <success>
- Evidence: <screenshot/command output reference>

## 11) Preventive Actions / Hardening
- <tuning, policy, documentation, training, monitoring, or standardization>

## 12) Escalation Notes (What I would escalate vs handle)
**Escalate if:**
- <condition>

**Handled at Helpdesk level because:**
- <reason>

## 13) Timeline (Optional)
- YYYY-MM-DD HH:MM — ticket opened
- YYYY-MM-DD HH:MM — triage started
- YYYY-MM-DD HH:MM — root cause identified
- YYYY-MM-DD HH:MM — resolved and validated

## 14) References
- Video/Resource: <title> (timestamp range if from YouTube)
- Commands/Docs used: <links or names>
