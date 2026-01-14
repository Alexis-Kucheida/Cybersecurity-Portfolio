# Incident Report (NIST CSF) — ICMP DoS Attack

## Overview
Case study of a Denial of Service (DoS) incident where an attacker flooded the network with ICMP packets through an unconfigured firewall, disrupting internal network services for approximately two hours.

## Scenario Summary
- Event: ICMP flood (DoS) against the company network
- Impact: Internal users could not access network resources for ~2 hours
- Root cause: Unconfigured firewall allowed excessive ICMP traffic
- Immediate actions: Blocked inbound ICMP, took non-critical services offline, restored critical services
- Improvements: ICMP rate limiting, source IP verification, monitoring, IDS/IPS filtering

## Skills Demonstrated
- Incident analysis and clear documentation
- Mapping actions to NIST CSF (Identify, Protect, Detect, Respond, Recover)
- Network security controls and traffic-based detections
- Operational recommendations (monitoring, runbooks, hardening)

## Artifacts
- Report (Markdown): `incident-report-icmp-dos-nist-csf.md`
- Report (PDF): `[PDF report](incident-report-icmp-dos-nist-csf.pdf)`

## NIST CSF Mapping (High-level)
- Identify: Asset inventory, exposure points, risk gaps
- Protect: Firewall hardening, baseline configuration, access control
- Detect: Monitoring and alerts for abnormal ICMP patterns
- Respond: Containment steps, communications, post-incident improvements
- Recover: Service restoration validation, recovery planning, lessons learned

## Notes
This is a portfolio case study. No real company identifiers, IP addresses, domains, or confidential details are included.
