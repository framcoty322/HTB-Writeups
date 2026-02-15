# 05 - PrivEsc: SeBackupPrivilege Abuse

**Objective:** Escalate from `svc_backup` to `Administrator` (Domain Admin) by abusing backup privileges to steal the Active Directory database (`ntds.dit`).

## 1. Initial Access & User Flag

Using the NTLM hash obtained from the LSASS dump, we connect via WinRM.

![](<attachments/Pasted image 20260107221942.png>)

- **User:** `BLACKFIELD\svc_backup`
- **Method:** Pass-the-Hash with Evil-WinRM

## 2. Privilege Enumeration

When checking the user’s privileges, we find a critical one enabled by default:

```powershell
whoami /priv
```

| Privilege | State | Description |
|---|---|---|
| SeBackupPrivilege | Enabled | Allows reading files while bypassing ACLs (for backups). |
| SeRestorePrivilege | Enabled | Allows writing files while bypassing ACLs. |

![](<attachments/Pasted image 20260107222105.png>)

**Why this matters:** Microsoft allows Backup Operators to copy system-locked files (like `NTDS.dit`) for maintenance purposes. An attacker can abuse this to extract hashes for **all** domain users.

`NTDS.dit` is used to dump all NT hashes from the Domain Controller. This is useful for **persistence** on the DC: even if passwords are changed later, old hashes can still be valuable depending on the context.

![](<attachments/Pasted image 20260107224210.png>)

We try to create a copy directly...

![](<attachments/Pasted image 20260107224241.png>)

…but it fails :)

![](<attachments/Pasted image 20260107224332.png>)

Then we remember we have `SeBackupPrivilege`.

---

## 3. Abusing SeBackupPrivilege with DiskShadow

To abuse this privilege, we first create a **logical drive**, for example `Z:`:

![](<attachments/Pasted image 20260107224500.png>)

Reference:
```text
https://pentestlab.blog/tag/diskshadow/
```

![](<attachments/Pasted image 20260107224644.png>)

We will use `DiskShadow`:

![](<attachments/Pasted image 20260107224828.png>)

On Kali, we copy the script and change `%someAlias%` → `framcoty`.

![](<attachments/Pasted image 20260107225016.png>)

First, we delete the last 3 lines because they are unnecessary.

![](<attachments/Pasted image 20260107225122.png>)
![](<attachments/Pasted image 20260107225234.png>)

We upload the file to the victim machine.

![](<attachments/Pasted image 20260107225312.png>)

Now we run:

```powershell
diskshadow.exe /s c:\Users\svc_backup\Documents\pwn.txt
```

![](<attachments/Pasted image 20260107230112.png>)

We notice we were missing an `s`, so we modify the file by adding a trailing space to each line (to prevent it from “eating” the last character when running), then upload it again.

![](<attachments/Pasted image 20260107230202.png>)
![](<attachments/Pasted image 20260107230308.png>)

Now we successfully create the copy using our privilege. If we list `Z:` we can see everything we would normally have on `C:\`.

![](<attachments/Pasted image 20260107230517.png>)

Now we can access:

```powershell
z:\Windows\NTDS\ntds.dit
```

![](<attachments/Pasted image 20260107230732.png>)

Using `copy` won’t work, so we use `robocopy`:

```powershell
robocopy /b z:\Windows\NTDS\ . ntds.dit
```

![](<attachments/Pasted image 20260107230923.png>)
![](<attachments/Pasted image 20260107230948.png>)

Now we have `ntds.dit` ready to transfer back to Kali.

---

## 4. Dumping hashes with impacket-secretsdump

We use `impacket-secretsdump`:

![](<attachments/Pasted image 20260107231311.png>)
![](<attachments/Pasted image 20260107232532.png>)

We download the required files to dump all user hashes from Active Directory.

![](<attachments/Pasted image 20260107232634.png>)

```bash
impacket-secretsdump -system system -ntds ntds.dit LOCAL
```

![](<attachments/Pasted image 20260107232757.png>)
