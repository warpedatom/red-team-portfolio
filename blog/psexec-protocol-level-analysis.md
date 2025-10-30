# PsExec — Protocol-Level Analysis & Operational Mechanics

**Author:** Velkris (Jared Perry)  
**Focus:** Protocol-level analysis and operational mechanics for remote command-execution tooling

## Summary
PsExec (Microsoft Sysinternals / similar implementations) is a remote administration capability commonly used to run commands on Windows hosts over SMB or RPC by creating remote services or leveraging administrative file shares. It is useful for legitimate administration and for red teams validating lateral-movement paths. This post describes how these tools operate at the protocol and platform levels, what defenders should log, and how to validate controls safely in a lab.

## Technical mechanics (conceptual)
- **Authentication & authorization:** PsExec-style tools use existing Windows authentication (NTLM, Kerberos) and require valid administrative credentials on the target host (local admin or domain-admin equivalence).
- **Remote service creation:** A common approach is to copy a small executable to an administrative share (e.g., `\\TARGET\ADMIN$\`), then create a remote service (via SCM/RPC) that runs that executable, which performs the requested command.
- **SMB & RPC usage:** These tools use SMB for file transfer/placement and RPC/Service Control Manager (SCM) APIs to create and start services remotely.
- **Fallback mechanisms:** When direct SMB/ADMIN$ access is not available, variants may attempt WMI, WinRM, or other remote-execution channels depending on tool capabilities and environment configuration.
- **Credential delegation:** Because the operation uses the caller’s credentials, any reuse or theft of those credentials can be used to pivot to other hosts.

> Conceptual overview only. Don’t perform remote admin actions without authorization.

## Observable behaviors & telemetry
- **ADMIN$ file writes:** Unexpected writes to administrative file shares, particularly from accounts/hosts that normally do not administer the target.  
- **Service SCM events:** Creation of short-lived remote services, service start/stop events, or scheduled tasks created remotely.  
- **SMB/RPC anomalies:** Unusual SMB connections to `ADMIN$`, service creation via RPC, or abnormal file copy patterns.  
- **Repeated auth attempts:** NTLM or Kerberos authentication attempts where credential reuse or delegation looks atypical.
- **Process execution trails:** Processes launched via remote services that spawn uncommon parent-child relationships.

## Detection signals (practical)
- Alert on `ADMIN$` writes that are not from known management stations or patch systems.  
- Monitor Windows event logs for remote service creation (Event IDs: Service control manager events) and correlate with process creation logs.  
- Detect abnormal SMB file operations from endpoints that do not normally perform administrative tasks.  
- Use EDR to flag remote service-created processes or child processes with network-, SMB-, or RPC-origin markers.

## Mitigations & hardening
- **Least privilege on admin shares:** Restrict ADMIN$ and other administrative shares to approved hosts and accounts; apply ACLs to limit access.  
- **PAM and ephemeral admin:** Use Privileged Access Management to grant temporary administrative rights rather than persistent local admin credentials.  
- **Network segmentation:** Limit which hosts can reach admin shares across the network and enforce management VLANs or zero-trust principles.  
- **Logging & monitoring:** Centralize SMB, RPC/SCM, and process creation logs and set rules for remote file placement or service creation.  
- **Harden remote-management channels:** Use secure, auditable management tools (e.g., managed orchestration, jump hosts) instead of ad-hoc remote service creation.

## Safe lab validation (non-actionable)
- In an isolated lab, simulate remote administrative workflows and validate that file-write, service-creation, and authentication events are captured in SIEM and EDR.  
- Test detection by emulating benign remote-management tasks from approved management workstations to ensure noisy, permitted activities do not trigger false positives.

## TL;DR
PsExec-style remote execution leverages SMB and SCM to run commands remotely. Defenders should monitor admin-share writes, remote service creation, and anomalous SMB/RPC activity; enforce least privilege and use managed admin access to reduce exposure.
