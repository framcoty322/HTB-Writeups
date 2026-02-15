![](<attachments/Pasted image 20260107122724.png>)

Now that we have potential Active Directory (AD) usernames, we test whether any of them are **AS-REP roastable** (Kerberos pre-auth disabled) using Impacket’s `GetNPUsers.py`:

`/usr/share/doc/python3-impacket/examples/GetNPUsers.py`

We use option **4. Request TGTs for users in a file**.

![](<attachments/Pasted image 20260107122910.png>)

![](<attachments/Pasted image 20260107122928.png>)

We find a hash, and we crack it with `john`.

![](<attachments/Pasted image 20260107123010.png>)

With these steps, we obtain the first valid credentials.

![](<attachments/Pasted image 20260107123139.png>)

The credentials work over **SMB**, but not over **WinRM**, so we need to enumerate further with `bloodhound` to get more information.
