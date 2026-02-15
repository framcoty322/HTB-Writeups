| **Target Object (Victim)** | **Object Type** | **Vulnerable ACL (Permission)** | **Who Has It (Attacker)** | **What the DACL Allows**                                     |
| -------------------------- | --------------- | ------------------------------- | ------------------------- | ------------------------------------------------------------ |
| **MANAGEMENT**             | Group           | `WriteOwner`                    | `judith.mader`            | Become the owner and rewrite permissions to join the group.  |
| **MANAGEMENT_SVC**         | User            | `GenericWrite`                  | `MANAGEMENT` group        | Modify user attributes (Shadow Credentials injection).       |
| **CA_OPERATOR**            | User            | `GenericAll`                    | `management_svc`          | Full control over the object (Shadow Credentials injection). |

**Objective:** Escalate privileges from the user `judith.mader` until obtaining membership in the `MANAGEMENT` group.

**Attack path:** JUDITH.MADER $\xrightarrow{\text{WriteOwner}}$ MANAGEMENT (Group)

## 1. Group ownership takeover (WriteOwner)

Since we have `WriteOwner`, the first step is to **claim ownership** of the **group** object.

- **What we do (high level):**
  1. Change the **Owner** of the `MANAGEMENT` group to `judith.mader`.
  2. Validate that ownership was applied (for example, by checking the object or in BloodHound after refreshing the ingestion).

- **Why it works:** `WriteOwner` explicitly authorizes us to change the owner on the object.

![](<attachments/Pasted image 20260105115419.png>)

## 2. DACL modification (grant ourselves control)

Once we are the owner, we can modify the **DACL** (access control list) of the group to grant ourselves permissions.

- **Goal of this phase:** Grant `judith.mader` permissions equivalent to **FullControl** (or, at minimum, `WriteMembers`) over the `MANAGEMENT` group.
- **Expected result:** After applying the new ACE, `judith.mader` is authorized to **modify the group's membership**.

![](<attachments/Pasted image 20260105115545.png>)

## 3. Add membership to the group

With control over the group, the final step is to **add ourselves** as members of `MANAGEMENT`.

- **Action:** Add `judith.mader` to the `MANAGEMENT` group.
- **Verification:** Confirm membership by listing the group's members (with whatever tool you are using in your environment).

![](<attachments/Pasted image 20260105115636.png>)
![](<attachments/Pasted image 20260105115713.png>)

---

## Current status

- User: `judith.mader`
- Groups: `Domain Users`, **`MANAGEMENT`** ✅
- Next step: Use this new membership to attack `management_svc` in the note [[04-shadow-creds-management.md]].
