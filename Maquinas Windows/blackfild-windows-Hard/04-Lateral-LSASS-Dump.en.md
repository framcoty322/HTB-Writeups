![](<attachments/Pasted image 20260107160701.png>)

With the credentials we obtained, we mount the `forensic` share to inspect its contents:

```bash
sudo mount -t cifs //10.129.229.17/forensic /mnt/smbmontura -o username=audit2020,password=newP@ssword2022
```

![](<attachments/Pasted image 20260107162404.png>)
![](<attachments/Pasted image 20260107220311.png>)

## Theory: What is LSASS and why is it critical?

In the screenshot, we see a file named `lsass.zip` inside a forensic analysis folder. This is a *gold mine* for an attacker.

### 1. What is LSASS?

**LSASS** (*Local Security Authority Subsystem Service*) is a critical Windows process (`lsass.exe`) responsible for:

- Enforcing security policies
- Validating user logons
- Managing password changes
- Creating access tokens

### 2. Why is it a target?

[cite_start]To improve user experience and enable access to network resources without constantly asking for passwords (Single Sign-On), **LSASS stores credentials in RAM**[cite: 238].

These credentials may appear in several formats:

- **Plaintext:** (If WDigest is enabled—rare on modern systems)
- **NTLM hashes:** Used for local and network authentication
- **Kerberos tickets (TGT/TGS):** Used in Active Directory environments
- **LM hashes:** (Very old systems)

### 3. The .DMP file (MiniDump)

The file we found (`lsass.zip`) contains a **memory dump** (`lsass.DMP`).

- **Context:** Someone (probably an admin or a forensic analyst) took a “snapshot” of the `lsass.exe` process memory while the server was running and saved it to disk.
- **Opportunity:** As attackers, we don’t need to run noisy tools like Mimikatz on the live server (which would trigger AV/EDR). We can simply steal this “offline” dump, bring it to Kali, and extract credentials locally.

> [cite_start]**Security note:** Credentials inside an LSASS dump only belong to users who had an **active session** at the time the dump was created[cite: 241].

![](<attachments/Pasted image 20260107220844.png>)

Using `pypykatz`, we can recover the credentials.

![](<attachments/Pasted image 20260107220923.png>)

We use the `minidump` option:

```bash
pypykatz lsa minidump lsass.DMP
```

![](<attachments/Pasted image 20260107221504.png>)

Thanks to `pypykatz`, we recover the NTLM hash (`NT`). In another terminal, we verify whether it is valid and whether it works to connect via `evil-winrm`.
