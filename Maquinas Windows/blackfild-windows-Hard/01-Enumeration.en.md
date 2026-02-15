![](<attachments/Pasted image 20260107122040.png>)

We see port **445** open, so we try to connect with invalid credentials to check whether there are shared resources available.

![](<attachments/Pasted image 20260107122237.png>)

```bash
nxc smb 10.129.229.17 -u 'loquesea' -p '' --shares
```

We can see we have **READ** permissions, so we create a mount point to inspect what’s inside:

```bash
sudo mount -t cifs //SERVER_IP/SHARE_NAME /mnt/smbmount -o username=guest,password=
```

![](<attachments/Pasted image 20260107122419.png>)

Thanks to this mount, we can browse everything from our Kali machine (as if it were a local folder). From there, we find usernames and copy everything into a working directory.

![](<attachments/Pasted image 20260107122540.png>)
