# 00 - Overview: Jeeves

## 🎯 Target Information
* **IP:** 10.10.10.63
* **OS:** Windows 10 (Build 10586)
* **Main Technique:** Jenkins Exploitation -> Pass-the-Hash -> Alternate Data Streams.

---

## 🔐 Credentials and Hashes

| User | Password / Hash | Context |
| :--- | :--- | :--- |
| **kohsuke** | *(Reverse shell access)* | Local user obtained after exploiting Jenkins and uploading `nc.exe`. |
| **KeePass** | `moonshine1` | Password cracked to unlock `CEH.kdbx`. |
| **Administrator** | `aad3b435b51404eeaad3b435b51404ee:e0fb1fb8...38cb` | NTLM hash extracted from KeePass (Entry: Backup stuff). |
...
## ⚡ Short Attack Path

1. **Reconnaissance:**
   * Port **50000** open running **Jetty**.
   * The `/askjeeves` directory was found, which hosts a **Jenkins** server.

2. **Foothold:**
   * Command execution via the **Jenkins Script Console**.
   * **AV Bypass:** `nc.exe` was uploaded/renamed using PowerSh... to evade the antivirus and get a reverse shell as `kohsuke`.

3. **Privilege Escalation:**
   * **Looting:** `CEH.kdbx` found in the `Documents` folder.
   * **Cracking:** Hash extracted with `keepass2john` and cracked with `john` (pass: `moonshine1`).
   * **Lateral Movement:** Using the Administrator NTLM hash to perform **Pass-the-Hash** and gain full access to the system.

4. **Post-Exploitation (Flags):**
   * The `root.txt` flag is hidden in an **Alternate Data Stream (ADS)** of the file `hm.txt`.
   * Command to read it: `powershell Get-Content -Path "hm.txt" -Stream "root.txt"`.
