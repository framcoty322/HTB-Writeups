#### **Description**

It was identified that the service account `svc-alfresco` in the HTB.LOCAL domain has the `UF_DONT_REQUIRE_PREAUTH` attribute configured (Do not require Kerberos pre-authentication), which allows performing an **AS-REP Roasting** attack.

#### **What is AS-REP Roasting?**

It is an attack that exploits Active Directory accounts that have Kerberos pre-authentication disabled. This allows an attacker to:

1. Request a TGT (Ticket Granting Ticket) without providing credentials.
    
2. Obtain an AS-REP hash encrypted with the user's password.
    
3. Perform an offline brute-force attack to crack the password.
    

#### **Severity**

🔴 **HIGH** - Allows obtaining valid domain credentials without prior authentication.

### **Tool Used**

**GetNPUsers.py** (Impacket Suite) ![](<attachments/Pasted image 20260101182602.png>) ![](<attachments/Pasted image 20260101182717.png>)
```bash
GetNPUsers.py -no-pass -usersfile users.txt contoso.com/
```

- **Purpose**: Identifies and exploits users with `UF_DONT_REQUIRE_PREAUTH` enabled.
    
- **Developer**: Fortra (formerly SecureAuth).
    
- **Version**: Impacket v0.13.0.dev0
    

![](<attachments/Pasted image 20260101182828.png>) ![](<attachments/Pasted image 20260101182852.png>)