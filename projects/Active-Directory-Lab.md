# Active Directory Pentest Lab

**Objective**  
Develop a multi-VM Active Directory environment to research enumeration, privilege escalation, and lateral movement techniques in a safe, isolated lab.

**Environment**  
- 1 Domain Controller, 2 Windows workstations (VirtualBox / VMware)  
- No external or production networks accessed  

**Approach**  
- Conducted enumeration (LDAP, SMB, WinRM) using standard red-team toolsets  
- Recreated Kerberoast and AS-REP roast attacks to study credential exposure  
- Mapped domain relationships and attack paths using BloodHound  
- Explored delegation abuse and pass-the-ticket workflows  

**Findings**  
- Identified excessive SPNs and weak service account permissions  
- Discovered lateral-movement opportunities due to unconstrained delegation  
- Highlighted insufficient event logging on DCs and workstation endpoints  

**Recommendations**  
- Enforce strong SPN account hygiene and Kerberos ticket rotation  
- Implement GPO-based hardening to restrict delegation and administrative logins  
- Increase log retention and visibility for 4768/4769/4624 events  

**Outcome**  
Delivered AD hardening playbook, BloodHound-based path analysis diagrams, and a reference lab guide for identity-attack simulations.
