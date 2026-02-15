# 06 - ADCS Enumeration: Finding ESC9

Objective: Identify vulnerable certificate templates on the Domain Controller.

Tool: Certipy (the Swiss Army knife for ADCS).

Credential: NTLM hash of `ca_operator` (obtained in note [[05-latmov-ca-operator]]).

## 🧩 Enumeration Theory (CPTS)

To attack ADCS, we need a valid domain user to read the PKI (Public Key Infrastructure) configuration stored in LDAP.

- We will use the `ca_operator` account because, based on its name, it likely has enrollment rights on restricted or interesting templates.

## 1. Vulnerability Discovery (Certipy Find)

We run `certipy find` to download the CA and template configurations. We use the `-vulnerable` option so the tool automatically filters known attack vectors (ESC1, ESC8, ESC9, etc.).

```bash
# Syntax: certipy find -u [USER] -hashes [NTLM] -dc-ip [IP] -vulnerable -stdout
# Replace <HASH_CA_OPERATOR> with the hash obtained in the previous step (b4b8...)

certipy-ad find -u ca_operator@certified.htb -hashes <HASH_CA_OPERATOR> -dc-ip 10.129.167.49 -vulnerable -stdout
```

![](<attachments/Pasted image 20260105122946.png>)

![](<attachments/Pasted image 20260105123007.png>)

## 2. Results Analysis

The output reveals a critical template named **`CertifiedAuthentication`**.

**Key output data:**

- **Template Name:** `CertifiedAuthentication`

- **Enabled:** `True`

- **Enrollment Rights:** `CERTIFIED.HTB\operator ca` (our user `ca_operator` has permission to request this certificate)2.

- **[!] Vulnerabilities:** `ESC9`3.

## 🧠 What is ESC9?

Unlike ESC1 (where we set the administrator name inside the certificate request), **ESC9** is more subtle:

1. **The issue:** The template uses the requester’s `userPrincipalName` (UPN) for identification, and the mapping configuration on the DC is insecure (`no security extension`).

2. **The attack:**

   - If we have write permissions over a user (like `ca_operator`), we can **temporarily change its UPN** in Active Directory (e.g., change it to `Administrator`).

   - We request the certificate. The CA reads the UPN (`Administrator`) and issues us a valid certificate for the Administrator.

   - This attack requires **write rights** over the user object that is requesting the certificate. (We already have this because `management_svc` has `GenericAll` over `ca_operator`.)

---

**Current Status:**

- **Vector identified:** Template `CertifiedAuthentication` vulnerable to **ESC9**.

- **Requirement:** Ability to modify `ca_operator`'s UPN.

- **Next step:** Execute ESC9 exploitation (UPN spoofing) to obtain Domain Admin access in the note [[07-privesc-adcs-esc9]].
