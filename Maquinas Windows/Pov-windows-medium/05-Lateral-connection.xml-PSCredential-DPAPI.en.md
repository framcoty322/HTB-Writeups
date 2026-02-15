![](<attachments/Pasted image 20260110003340.png>)
we found a `connection.xml` file
![](<attachments/Pasted image 20260110003356.png>)
	![](<attachments/Pasted image 20260110003411.png>)
	

```powershell
$cred = Import-Clixml -Path "C:\users\sfitz\Documents\connection.xml"
```
![](<attachments/Pasted image 20260110003716.png>)
```powershell
$cred.GetNetworkCredential().password
```
