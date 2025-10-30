# Golden Ticket Attacks: Theory, Detection, and Defense

**Author:** Velkris (Jared Perry)  
**Focus:** Forged Kerberos TGT attacks and their defensive implications

## Summary
A Golden Ticket is a forged Kerberos Ticket Granting Ticket (TGT) that allows an attacker persistent, often stealthy, access to domain resources by impersonating arbitrary domain principals for as long as the forged ticket remains valid. These attacks are enabled by compromise of the Active Directory domain's Kerberos Key Distribution Center (KDC) secret(s), typically the KRBTGT account credentials.

## How it works (conceptual)
- The KDC signs Kerberos TGTs using the KRBTGT account key. If an adversary obtains the password hash for KRBTGT (or a copy of the KDC keys), they can generate valid-looking TGTs with arbitrary privileges.
- A forged TGT (golden ticket) can be crafted to impersonate any user, set arbitrary expiration times, and grant domain-level capabilities.

Key point: a Golden Ticket gives the attacker durable impersonation until defenders rotate the KRBTGT keys or detect and remove the malicious artifacts.

## Defensive detection signals
- Sudden use of accounts at odd times or from unusual hosts with elevated privileges.
- Unusual Kerberos ticket lifetimes or non-standard renewal patterns.
- Anomalous use of high-privilege operations without corresponding account provisioning events.
- Correlate privileged account actions, lateral movement artifacts, and persistence indicators.

## Mitigations & hardening
- Protect KRBTGT account and domain controllers with strict access controls, monitoring, and host hardening.
- Implement tiered administrative model to limit exposure of domain-level secrets.
- Rotate KRBTGT keys (2-phase rotation) if compromise is suspected—know that rotation is operationally disruptive and must be planned.
- Employ strong endpoint and network detections (EDR, network micro-segmentation) to reduce the chance of initial compromise.

## Safe lab validation (non-actionable)
- Use an isolated AD testbed to exercise detection rules for long-lived TGT usage and unusual authentication patterns.
- Test monitoring and response playbooks that rely on cross-source telemetry (EDR + AD/SIEM) rather than attempting to recreate a golden ticket in a production-like environment.

## TL;DR
Golden Tickets are forged TGTs that allow impersonation across an AD domain. Protect KDC secrets, tier administrative accounts, and monitor for anomalous ticket usage and privileged activity.
