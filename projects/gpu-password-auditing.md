# GPU-Accelerated Password Auditing

**Objective**  
Evaluate GPU performance improvements for password auditing and recovery workflows in controlled simulations.

**Environment**  
- NVIDIA RTX 3080 (CUDA) running Hashcat  
- Tested NTLM, SHA-1, SHA-256, and Kerberos hashes  

**Findings**  
- Achieved ~100 GH/s for NTLM compared to ~25 GH/s CPU-only benchmarks  
- Demonstrated ~4× performance increase in password-recovery timelines  

**Outcome**  
Created optimized workflows and validated scripts for password auditing, supporting lab-based adversary simulations and certification prep.
