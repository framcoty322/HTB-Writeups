# How to use it on this machine (Step by Step)

Based on your Nmap scan, we know the domain is `htb.local`. Let's build the command.

**The base command:**


```bash
ldapsearch -x -H ldap://<IP> -b "<BASE_DN>"
```

![[Pasted image 20251231110604.png]]

### What to look for in the output?

You will receive a "waterfall" of text (huge output). What you are interested in finding amidst all that text are **usernames**.

If you want to filter out the noise and see only the users, you can modify the command like this:


```bash
ldapsearch -x -H ldap://10.129.29.201 -b "dc=htb,dc=local" "(objectClass=user)" sAMAccountName
```

![[Pasted image 20251231111245.png]] ![[Pasted image 20251231111336.png]]