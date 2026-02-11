# Incident Report: ICMP DoS Attack (NIST CSF)

## Summary
The company experienced a denial-of-service incident in which network services stopped responding due to a flood of incoming ICMP packets. The internal network was unavailable for approximately two hours, preventing employees and clients from accessing network resources and disrupting business operations and customer service. The incident response team mitigated the event by blocking inbound ICMP traffic, taking non-critical services offline to reduce load, and restoring critical services first.

---

## Identify
The cybersecurity team determined the incident was caused by an ICMP flood (DoS/DDoS) launched through an unconfigured firewall. Because ICMP operates at the network layer (OSI Layer 3), the attacker overwhelmed the network by sending a continuous stream of ICMP echo requests. Systems and network devices had to process and answer these requests (echo replies), consuming bandwidth and resources until normal traffic could no longer reach internal services. The impact affected the entire internal network, including critical services required for normal operations.

---

## Protect
To reduce the likelihood and impact of similar incidents, the network security team implemented:
- Firewall rate-limiting rules to restrict the volume of incoming ICMP packets.
- Source IP verification / anti-spoofing controls on the firewall to identify and block ICMP traffic with suspicious or forged source addresses.
- IDS/IPS filtering rules to drop or throttle ICMP traffic that matches malicious patterns.

---

## Detect
To improve early detection and shorten response time, the organization implemented:
- Network monitoring and alerting to detect abnormal traffic spikes (e.g., sudden ICMP rate increases, bandwidth saturation, interface errors).
- IDS/IPS monitoring to flag suspicious ICMP characteristics and generate alerts for investigation.
- Logging improvements (firewall + IDS/IPS logs) to support faster triage and clearer incident timelines.

---

## Respond
For future events, the cybersecurity team will follow a defined response process:
- Containment: immediately rate-limit or block the offending traffic (ICMP) at the firewall and isolate affected segments if necessary.
- Service prioritization: temporarily stop non-critical services to reduce internal load and keep essential services available.
- Investigation: analyze firewall/IDS/IPS logs and traffic patterns to confirm attack type, scope, and possible spoofing.
- Communication and reporting: document the incident, notify management, and escalate to legal/authorities if required by policy or regulation.
- Process improvement: maintain hardened firewall baselines, enforce change control, and train staff on incident procedures aligned with the NIST CSF.

---

## Recover
Recovery focused on restoring normal operations in a controlled order:
- Restore critical services first, then return non-critical services once traffic stabilizes.
- Validate network performance (latency, bandwidth, availability) and confirm normal access for internal users and clients.
- Communicate transparently with clients about the outage, apologize for the disruption, and explain the key security improvements implemented (rate limiting, anti-spoofing checks, monitoring, IDS/IPS) to reinforce trust and reduce the likelihood of recurrence.

