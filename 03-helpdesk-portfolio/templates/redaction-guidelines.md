# Redaction Guidelines (GitHub Portfolio)

## 1) Goal
Publish real-looking technical work without exposing secrets, personal data, or identifiable infrastructure.

## 2) Never show (remove or replace)
- Passwords, secrets, API keys, tokens, recovery codes
- Product keys / license keys
- Real personal data: full names, emails, phone numbers, addresses
- Real company or client names (unless you have explicit permission)
- Public IPs, router serials, WAN details, identifiable hostnames
- Internal share names that reveal sensitive info (e.g., “Finance_Salaries”)
- Exact domain names used in real environments

## 3) What is safe to show (generally)
- Generic lab domains (e.g., `LAB.LOCAL`, `corp.local`)
- Private RFC1918 IP ranges (e.g., `192.168.x.x`) for a lab
- Non-sensitive hostnames (e.g., `DC01`, `FILE01`, `WIN11-CLIENT01`)
- Command outputs with sensitive lines removed
- Event IDs and timestamps (lab), with usernames anonymized

## 4) Use consistent fake identities
Use placeholder identities across all artifacts:
- Users: `test.user1`, `j.smith`, `it.admin`
- Devices: `WIN11-CLIENT01`, `DC01`, `FILE01`
- Domain: `LAB.LOCAL`
- Shares: `\\FILE01\Shared`, `\\FILE01\HR-Shared` (avoid real org names)

## 5) How to redact (practical methods)
### Screenshots
- Crop to only what matters (proof point)
- Blur/paint over:
  - usernames if personal
  - IPs if public/identifiable
  - domain names if real
  - serial numbers
- Keep UI context so the screenshot is still believable.

### Command output
- Paste only relevant lines.
- Replace sensitive items:
  - `john.doe@company.com` → `test.user1@lab.local`
  - `COMPANY-DC.company.com` → `DC01.lab.local`
- Use `[...]` to indicate omitted sections.

### Logs
- Include only the events that support the conclusion.
- Replace usernames and hostnames with your placeholders.

## 6) Redaction checklist (before committing)
- [ ] No secrets/keys/passwords in text or images
- [ ] No product keys/license identifiers
- [ ] No real personal data
- [ ] No real company/client identifiers
- [ ] No public IPs / WAN details
- [ ] Placeholders used consistently
- [ ] Evidence still understandable after redaction

## 7) Evidence naming convention
Store evidence per ticket/runbook:
- `tickets/evidence/TKT-###-<shortname>-01.png`
- `tickets/evidence/TKT-###-<shortname>-02.png`

Example:
- `tickets/evidence/TKT-006-share-access-denied-01.png`

## 8) Disclaimer (optional, recommended)
Add this line to your repo README or each artifact footer:
> All scenarios are performed in a personal lab environment. Any names, domains, and identifiers are fictional and used for demonstration only.
