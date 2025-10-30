# Kerberoasting: Concept, Detection, and Defensive Controls

**Author:** Velkris (Jared Perry)  
**Focus:** Identity-based credential theft in Kerberos environments

## Summary
Kerberoasting is an offline credential-theft technique that targets service accounts in Active Directory by requesting service tickets (TGS) for service principal names (SPNs), extracting the encrypted portion of the ticket, and attempting to crack the corresponding service-account password offline. It exploits the fact that Kerberos service tickets contain keys derived from the service account password.

## How it works (conceptual)
- A client requests a service ticket (TGS) for an SPN from the Ticket Granting Service (TGS) using a valid Kerberos TGT.
- The TGS issues a ticket encrypted with the service account’s key.
- An attacker with the ticket copies the encrypted data and tries to recover the plaintext password offline using password-cracking techniques.

Key point: the attack does not require domain credentials beyond the ability to request service tickets; it becomes powerful when service accounts have weak passwords or poor rotation practices.

## Defensive detection signals
- High volume of TGS requests for many distinct SPNs originating from a single account or host.
- Unusual TGS requests outside normal usage patterns (time of day, source host).
- Repeated Kerberos error events (if cracking attempts cause abnormal replay or misuse).
- Windows event IDs of interest (for monitoring and hunting): TGS requests (Event IDs around 4768/4769), authentication anomalies, and privileged account activity.
- SIEM/hunting detection: watch for enumeration-like behavior where a user account requests many service tickets without initiating matching service connections.

## Mitigations & hardening
- Enforce strong, unique service-account passwords and rotate them frequently.
- Avoid using domain or high-privilege accounts for services—use Managed Service Accounts or Group Managed Service Accounts where possible.
- Disable unnecessary SPNs and remove unused service accounts.
- Implement privileged access management (PAM) and least privilege for service accounts.
- Increase logging and centralize collection of Kerberos-related events and authentication telemetry.

## Safe lab validation (non-actionable)
- Build an isolated AD testbed with representative SPNs and service accounts.
- Validate detection logic by simulating ticket request patterns at a high level (do **not** use any exploit or cracking tool in production).
- Ensure your SIEM produces alerts for anomalous TGS request volumes and that detection rules match expected noise levels.

## TL;DR
Kerberoasting exploits weak service-account passwords via offline cracking of Kerberos service tickets. Mitigate with strong password hygiene, account hardening, and monitoring for abnormal TGS request behavior.
