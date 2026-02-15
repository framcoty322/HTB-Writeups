# Blackfield - Overview

- **IP:** 10.10.10.192
- **OS:** Windows (Domain Controller)
- **Domain:** BLACKFIELD.LOCAL
- **Difficulty:** Hard
- **Key Techniques:**
    - SMB Enumeration (Guest Access)
    - AS-REP Roasting (Kerberos Pre-Auth Disabled)
    - ACL Abuse (ForceChangePassword)
    - LSASS Memory Dump Analysis (Pypykatz)
    - Privilege Escalation: `SeBackupPrivilege` (Backup Operators abuse)
    - NTDS.dit Dumping (WBAdmin & DiskShadow)

## Collected Credentials

| User | Password/Hash | Source | Permissions |
|---|---|---|---|
| `support` | `#00^BlackKnight` | AS-REP Roast | Domain User |
| `audit2020` | `H@CKTHEBOX#` (set by us) | ACL Abuse | Access to the 'forensic' share |
| `svc_backup` | NTLM Hash | LSASS Dump | Backup Operators / WinRM |
| `Administrator` | NTLM Hash | NTDS Dump | Domain Admin |
