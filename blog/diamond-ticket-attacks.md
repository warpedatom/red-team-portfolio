# Diamond Ticket Attacks: What They Are and How to Defend

**Author:** Velkris (Jared Perry)  
**Focus:** NTLM/PAC manipulation and Kerberos ticket forging variants

## Summary
The term "Diamond Ticket" is used to describe a specialized Kerberos attack where an attacker forges service tickets that include altered PAC (Privilege Attribute Certificate) data or leverages alternative signing relationships to escalate or persist. It is conceptually similar to Golden Ticket attacks but targets service tickets (TGS) and can involve manipulation of privilege attributes for lateral access.

## How it works (conceptual)
- Service tickets (TGS) include authorization data used by services to make access decisions (e.g., PAC).
- If an attacker can generate or manipulate tickets and associated signing keys, they can craft tickets that present elevated privileges to applications or services.
- Diamond Ticket scenarios often hinge on more nuanced trust relationships, such as compromised service accounts or delegation settings.

## Defensive detection signals
- Unexpected privilege assertions observed at application or service level despite account configurations that should prevent such elevation.
- Audit logs showing unusual PAC or token attributes, or service-auth behavior that does not match access-control lists (ACLs).
- Increased activity around service accounts or hosts that handle ticket decryption or authorization.

## Mitigations & hardening
- Lock down service accounts with minimal privileges and avoid unconstrained delegation where possible.
- Enforce strong controls around identity-signing keys and any systems that can issue or interpret PAC data.
- Monitor for abnormal PAC values or token attributes being presented to services.

## Safe lab validation (non-actionable)
- Establish a sandbox to instrument authentication paths and log the PAC/authorization attributes presented to services when tickets are used—validate that alerts trigger for deviations from expected values.
- Focus tests on detection engineering: can your SIEM/ticketing monitoring surface PAC anomalies without exposing production systems?

## TL;DR
Diamond Ticket-style attacks manipulate authorization data within Kerberos tickets. Reduce risk by hardening service accounts, restricting delegation, protecting signing keys, and monitoring for anomalous authorization attributes.
