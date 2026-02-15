![](<attachments/Pasted image 20260102191802.png>)

We upload all the files
![](<attachments/Pasted image 20260102191847.png>)

Our first question is .

- Since we have a p.agila user, let's see what it gives us access to.
![](<attachments/Pasted image 20260102192820.png>)
SERVICE ACCOUNT MANAGERS <--Service account administrator members

Let's take a look at Outbound Access Control.

![](<attachments/Pasted image 20260102193003.png>)


![](<attachments/Pasted image 20260102193200.png>)

Since my user p.agila is a member of the “SERVICE ACCOUNT” service accounts,

we will add it to that service.
![](<attachments/Pasted image 20260102193318.png>)

Assuming that service is already pwned, we will put it in “Add to Owned” to see what else we can do next.
![](<attachments/Pasted image 20260102193458.png>)

As we can see, we have generic rights in three groups #GenericWrite.


Since we have generic rights “GenericWrite,” this will give us permission to change those users' passwords. The tempting one is winrm.
  

I'm going to use a hidden password, with a section start certificate, without updating the password.
  

whenever you can, use the hidden password, because if you update it, the processes may be interrupted.  

That's why it's not good to change it, so I'm going to avoid doing so.
