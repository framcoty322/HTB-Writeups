![[Pasted image 20260101194432.png]]

Initially, the user we created does not work for the **DCSync attack**.

We need to successfully dump the entire **NTDS.DIT**.

What we will do is assign the user `s4vitar` to the **EXCHANGE WINDOWS PERMISSIONS** group.

1. We will detect the groups to add the user `s4vitar` to that specific group.
    

![[Pasted image 20260101194807.png]]

We are going to add user `s4vitar` to that group.

![[Pasted image 20260101194923.png]]


```bash
net group "Exchange Windows Permissions" s4vitar /add
```

We will verify that our user `s4vitar` has been added to that group.

![[Pasted image 20260101195016.png]]


```bash
net user s4vitar
```