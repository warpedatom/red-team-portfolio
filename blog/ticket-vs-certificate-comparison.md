# Ticket vs Certificate Attacks: Comparison and Defensive Strategies

**Author:** Velkris (Jared Perry)  
**Focus:** Differences and defensive approaches for identity-based attacks

## Summary
Ticket-based attacks (Golden, Diamond, Kerberoasting) and certificate-based attacks (Golden Certificate) both target identity systems but operate at different layers and with different operational tradeoffs. This post compares them and outlines detection and mitigation strategies for each.

## Core differences (conceptual)
- **Mechanism**
  - **Tickets (Kerberos):** Rely on Kerberos protocol artifacts (TGT/TGS) and the keys that sign them (e.g., KRBTGT). Attacks typically involve forging or stealing ticket material or cracking encrypted ticket content.
  - **Certificates:** Rely on PKI issuance and trust; abuses involve creating or misusing certificates that are trusted by services.

- **Persistence**
  - **Tickets:** Golden Tickets can be durable until KRBTGT keys are rotated; TGS-based attacks may require frequent recreation unless long-lived keys are used.
  - **Certificates:** Misissued certificates can be valid for their lifespan and can be made long-lived if policy allows—rotation and validity windows control persistence.

- **Detection complexity**
  - **Tickets:** Detection often needs correlation between AD authentication logs, abnormal ticket lifetimes, and privileged activity. Ticket anomalies can be subtle.
  - **Certificates:** Detection requires PKI/CA telemetry plus auth logs. Unexpected issuance or unusual subject/issuer fields are red flags.

## Defensive overlaps and differences
- **Hardening overlap**
  - Protect signing keys (KRBTGT keys and CA keys) with strict access controls.
  - Enforce least privilege for service and administrative accounts.
  - Ensure robust logging and centralized telemetry for both authentication types.

- **Unique defenses**
  - **Kerberos:** Rotate KRBTGT keys when compromise is suspected, enforce managed service accounts, and monitor TGS/TGT requester patterns.
  - **Certificates:** Use HSM-backed CAs, restrict certificate template permissions, monitor CA issuance, and enforce short validity periods.

## Detection strategy (recommended)
- Correlate logs across identity systems: AD authentication logs (Kerberos events), PKI issuance logs, EDR telemetry, and network logs.
- Hunt for patterns: high-volume ticket requests, unexpected certificate issuances, or long-lived auth artifacts.
- Implement behavior-based detection that focuses on anomalies rather than specific indicators.

## Lab validation (non-actionable)
- Build isolated test environments for Kerberos and PKI to exercise detection pipelines.
- Validate that your SIEM raises alerts for anomalous ticket patterns and unexpected certificate issuance events.

## TL;DR
Tickets and certificates are both identity primitives but differ in mechanics and detection needs. Protect signing keys, reduce credential lifespans, and centralize telemetry to detect and respond to both classes of attacks effectively.
