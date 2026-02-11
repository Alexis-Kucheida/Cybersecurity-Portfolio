# Internal Security Audit (Case Study) — Botium Toys 

## Objective
Conduct an internal IT security audit based on a fictional company scenario to assess risk, evaluate existing controls, and identify compliance gaps related to PCI DSS, GDPR, and SOC-style controls.

## Scope
- Environment: one physical location (office + storefront + warehouse) with a growing e-commerce presence.
- Audit focus: IT-managed assets, current security posture, and control/compliance coverage.
- Note: This is a fictional case study from a training exercise.

## Key Assets (from the scenario)
- On-prem systems and employee endpoints
- Internal network and internet connectivity
- Data retention and storage systems
- E-commerce / inventory / business applications
- Legacy systems requiring monitoring

## Risk Summary
- Overall risk score (scenario): **8/10**
- Primary drivers of risk:
  - Weak access control governance (least privilege, separation of duties)
  - Lack of encryption for sensitive data
  - Limited detection and resilience capabilities (IDS, backups, disaster recovery)

## Controls Assessment (high-level)
I reviewed the scenario and completed a controls checklist to evaluate coverage across:
- Access control
- Data protection
- Detection and monitoring
- Resilience and recovery
- Physical security

### Top Control Gaps Identified
- Least privilege (access not restricted to job needs)
- Separation of duties (insufficient segregation of responsibilities)
- Password policy and credential management (insufficient strength/standards)
- Encryption (sensitive data not encrypted at rest/in transit as expected)
- Intrusion detection / monitoring (no IDS or equivalent visibility)
- Business continuity: backups and disaster recovery planning

### Existing Controls Noted
- Firewall and endpoint anti-malware controls
- Physical security controls (e.g., locks/CCTV)

## Compliance Assessment (high-level)
I mapped the situation to common compliance expectations:
- PCI DSS (payment card data protection)
- GDPR (EU personal data handling and breach notification readiness)
- SOC-style controls (security governance, access control, change management, monitoring)

### Main Compliance Risks
- Sensitive data is not adequately encrypted.
- Data is not properly classified/inventoried, reducing control effectiveness.
- Access to sensitive data is not sufficiently restricted or monitored.

## Prioritized Recommendations
### Quick wins (0–30 days)
- Enforce least privilege (role-based access) and implement separation of duties for sensitive functions.
- Strengthen password policy (length, complexity, lockout) and enable MFA where possible.
- Deploy a password manager and standardize credential handling procedures.

### Medium term (30–90 days)
- Build an asset inventory and data classification baseline (systems, applications, sensitive datasets).
- Implement centralized logging and monitoring; deploy IDS or equivalent detection capability.
- Implement encryption for sensitive data (at rest and in transit) and tighten access controls to encrypted stores.

### Longer term (90+ days)
- Establish a backup strategy (frequency, retention, testing) and implement a documented disaster recovery plan with restore testing.

## Evidence / Deliverables
- Completed checklist: [controls-and-compliance-checklist.pdf](./controls-and-compliance-checklist.pdf)
- Source scenario notes: [scope-goals-risk-assessment.pdf](./scope-goals-risk-assessment.pdf)

## What I Learned
- How to think with an audit mindset (controls, gaps, risk).
- How to prioritize actions based on impact and feasibility.
- How compliance expectations influence security requirements and evidence.


