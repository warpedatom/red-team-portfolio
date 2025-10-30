# Rubeus — Protocol-Level Analysis & Operational Mechanics

**Author:** Velkris (Jared Perry)  
**Focus:** Protocol-level analysis and observable operational mechanics for Kerberos tooling

## Summary
Rubeus is a C# toolkit that manipulates Kerberos protocol primitives and Windows authentication APIs. It is used by red teams to exercise Kerberos functionality (request/renew/inject tickets, S4U flows, pass-the-ticket) and by defenders to validate telemetry and detection coverage. This post explains what Rubeus interacts with at the protocol and platform layers, what observable behaviors defenders should care about, and safe ways to validate coverage in an isolated lab.

## Technical mechanics (conceptual)
- **Kerberos messages:** Rubeus crafts and parses ASN.1-encoded Kerberos messages (AS-REQ, AS-REP, TGS-REQ, TGS-REP). It can perform legitimate Kerberos exchanges against a KDC or construct tickets offline for analysis in a sandbox.
- **Ticket blobs:** Kerberos tickets are binary blobs containing encrypted authorization data and PACs. Rubeus can extract, serialize, modify, and re-inject these blobs into Windows authentication contexts.
- **LSA / SSP integration:** On Windows, Kerberos tickets and tokens are surfaced via LSA and the Kerberos Security Support Provider. Rubeus leverages these APIs or memory interfaces to present tickets to the OS and services.
- **S4U flows & delegation:** Rubeus can exercise S4U2Self and S4U2Proxy flows to request tickets on behalf of users in constrained delegation scenarios — useful for validating delegation boundaries and telemetry.
- **Pass-the-ticket (PTT) operations:** Rubeus can inject tickets into session contexts so subsequent outbound connections use that ticket; this changes the principal associated with the session without a new interactive logon.

> Note: This is a conceptual overview for defenders. Do not use these descriptions to attempt misuse on systems you do not own or have authorization to test.

## Observable behaviors & telemetry
- **Bursty TGS requests:** Many TGS requests for distinct SPNs originating from a single account or host.
- **S4U-related activity:** Frequent S4U2Self or S4U2Proxy operations from hosts that do not normally request delegation.
- **Ticket injection artifacts:** Sessions whose authenticated principal changes without an interactive logon event, or services receiving authentication with unexpected ticket lifetimes.
- **Abnormal Kerberos lifetimes:** Tickets issued with non-standard validity or unusually long renewal patterns.
- **Process-level indicators:** Non-standard binaries invoking Kerberos APIs or interacting with LSA memory. EDRs may flag suspicious use of `LsaLogonUser`, `LsaCallAuthenticationPackage`, or direct process memory access to LSASS.

## Detection signals (practical)
- Correlate AD logs (TGS/TGT events) with network service connections to find mismatches.
- Alert on high-volume, short-window TGS requests from individual accounts.
- Monitor for S4U calls from non-service hosts or from accounts that never normally perform delegation.
- Use EDR to detect attempts to read LSASS memory or call LSA/Kerberos APIs from unusual processes.
- Baseline ticket lifetime and renewal behavior and alert on deviations.

## Mitigations & hardening
- Enforce least privilege for service accounts and use gMSA when possible.  
- Minimize exposed SPNs and remove unused service accounts.  
- Limit and monitor delegation (prefer constrained delegation and review delegation ACLs).  
- Protect domain controllers and KRBTGT secrets; restrict administrative access and instrument DCs with host-based EDR.  
- Centralize Kerberos telemetry in SIEM and correlate with EDR and network logs.

## Safe lab validation (non-actionable)
- Use a fully isolated AD lab to generate representative Kerberos traffic and test detection rules.  
- Validate that your SIEM raises alerts on anomalous TGS patterns, S4U misuse, and ticket injection-like session anomalies.  
- Do **not** run offensive operations against production systems. Keep lab keys and artifacts contained and rotate them between exercises.

## TL;DR
Rubeus operates at the Kerberos protocol and Windows authentication layers. Defenders should focus on anomalous Kerberos request patterns, delegation misuse, ticket lifetimes, and process-level interactions with LSA to detect misuse.
