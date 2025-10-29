# Threat Intelligence & ATT&CK Mapping Project

**Objective**  
Correlate real-world adversary TTPs with MITRE ATT&CK techniques through open-source threat reports and lab-based validation.

**Sources**  
- APT28 (Fancy Bear) and APT29 (Cozy Bear) public reports  
- MITRE ATT&CK Enterprise matrix  

**Approach**  
- Parsed OSINT reports to extract ATT&CK TIDs (T1059, T1555, etc.)  
- Replicated relevant behaviors in a controlled lab (execution, credential access, persistence)  
- Compared mapped techniques to defensive telemetry to evaluate detection coverage  

**Outcome**  
Produced mapping tables aligning observed behaviors to MITRE techniques, helping define coverage gaps and prioritize detection engineering.
