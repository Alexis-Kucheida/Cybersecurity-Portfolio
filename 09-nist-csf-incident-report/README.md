# Incident Report (NIST CSF) — ICMP DoS Attack

## Overview
Case study of a Denial of Service (DoS) incident where ICMP flooding disrupted internal network services for ~2 hours due to an unconfigured firewall.

## Skills Demonstrated
- Incident analysis and documentation
- NIST CSF mapping: Identify, Protect, Detect, Respond, Recover
- Network controls: ICMP rate limiting, anti-spoofing checks, monitoring, IDS/IPS

## Artifacts
- Markdown report: `incident-report-icmp-dos-nist-csf.md`
- PDF report: `incident-report-icmp-dos-nist-csf.pdf` 

## Key Takeaways
- Root cause: insufficient firewall configuration against ICMP flood traffic
- Improvements: rate limiting, source validation, monitoring/alerting, IDS/IPS filtering

