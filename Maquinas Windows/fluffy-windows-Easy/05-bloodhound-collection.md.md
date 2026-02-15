![](<attachments/Pasted image 20260102191000.png>)

Confirming that the decrypted credentials are correct, we will use bloodhound-python from our Kali to obtain information via bloodhound.
![](<attachments/Pasted image 20260102191311.png>)
```bash
bloodhound-python -c All -u 'p.agila' -p 'prometheusx-303' -ns 10.129.232.88 -d fluffy.htb
```

