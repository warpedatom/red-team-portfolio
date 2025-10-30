# Mimikatz — Protocol-Level Analysis & Operational Mechanics

**Author:** Velkris (Jared Perry)  
**Focus:** Protocol-level analysis and operational mechanics for credential-handling tooling

## Summary
Mimikatz is a toolset that exposes how Windows stores and manages authentication artifacts—LSASS memory structures, Kerberos ticket caches, and credential stores. It is frequently used in lab exercises to demonstrate abuses of credential handling and to validate detection. This post explains the internal concepts without providing actionable exploitation steps, highlights observable defensive signals, and recommends safe validation practices.

## Technical mechanics (conceptual)
- **LSASS memory and credential objects:** Windows stores credential materials (plaintext, NT hashes, Kerberos tickets) in LSASS and related LSA structures. Mimikatz parses those in-memory structures to extract credentials.
- **Privilege usage:** Many Mimikatz operations require elevated privileges (SeDebugPrivilege or SYSTEM) because they read other processes’ memory or interact with secure OS components.
- **SSPI / Kerberos interaction:** Mimikatz can parse, export, and manipulate Kerberos tickets and PACs; it uses or mimics SSPI interactions to present or inject credentials.
- **Token and impersonation operations:** The tool can create or manipulate Windows tokens to impersonate other users or escalate privileges in a session context.
- **Certificate/PKI access:** Mimikatz can enumerate certificate stores and, when privileges allow, extract certificate-related private key material from memory or accessible stores.

> High-level only. Do not use this to perform credential theft or unauthorized access.

## Observable behaviors & telemetry
- **LSASS memory reads:** Processes attempting to open and read LSASS process memory (a primary detection vector).  
- **Privilege acquisition:** Elevations to SeDebugPrivilege or unusual use of SYSTEM-context processes.  
- **Credential export artifacts:** Files created that contain credential-like structure or attempts to exfiltrate such files.  
- **Unexpected token changes:** Sessions switching principals without standard logon sequences.  
- **EDR alerts:** Calls to Windows APIs known to be used for credential access (e.g., `OpenProcess(PROCESS_VM_READ)`, `ReadProcessMemory`) from atypical binaries.

## Detection signals (practical)
- Block or alert on attempts to read LSASS memory except for approved processes (and monitor those exceptions).  
- Monitor for unusual privilege escalations and process parentage trees (e.g., script spawns that lead to suspicious binaries).  
- Detect creation or movement of files that contain credential artifacts using DLP/EDR heuristics.  
- Correlate authentication anomalies with endpoint events: credential extractions followed by lateral authentication attempts are high-risk indicators.

## Mitigations & hardening
- **LSA protection & Credential Guard:** Enable LSA protection, Windows Defender Credential Guard, and other OS features that isolate secrets from user-mode reads.  
- **EDR process protections:** Configure EDR to prevent unauthorized processes from opening LSASS or to terminate/alert on such attempts.  
- **Privileged Access Management (PAM):** Minimize users who can gain SYSTEM or similar rights and use ephemeral access where possible.  
- **Network controls & MFA:** Reduce the value of extracted credentials by enforcing MFA for remote access and by applying network segmentation to limit lateral use.  
- **Credential hygiene:** Rotate high-value credentials and use managed accounts (gMSA) for services.

## Safe lab validation (non-actionable)
- In an isolated lab, run controlled demonstrations with EDR/telemetry enabled to validate that LSASS access and privilege escalation generate alerts.  
- Ensure lab artifacts (hashes, tickets, certificates) are not reused outside the environment and that all test systems are rebuilt or rotated.

## TL;DR
Mimikatz reads and interprets OS-level credential artifacts from LSASS and related stores. Defenders should block LSASS reads, enable Windows-provided protections, monitor for privilege escalation, and correlate endpoint events with authentication anomalies.
