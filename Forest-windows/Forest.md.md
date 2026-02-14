![[Pasted image 20251231102434.png]]
# Forest (Windows)

**Tags:** #Windows #Easy #ActiveDirectory #ASREPRoasting #BloodHound #AccountOperators #DCSync

## 📋 Machine Information

| **Attribute**  | **Value**    |
| -------------- | ------------ |
| **OS**         | Windows      |
| **Difficulty** | Easy         |
| **Points**     | 0            |
| **Release**    | 12 Oct 2019  |
| **IP**         | 10.10.10.161 |

---

## 🎯 Topics Covered

In this machine, we will be covering the following points:

### 🔍 Enumeration

- **AXFR** - Domain Zone Transfer Attack (Failed)
    
- **RPC Enumeration** - Getting valid domain users
    
- **BloodHound Enumeration**
    
- **Gathering system information** with `SharpHound.ps1` - PuckieStyle
    
- **Representing and visualizing data** in BloodHound
    
- **Finding an attack vector** in BloodHound
    

### ⚔️ Exploitation

- **Performing an AS-RepRoast Attack** with the obtained users
    
- **Cracking Hashes**
    
- **Abusing WinRM** - EvilWinRM
    

### 🚀 Privilege Escalation

- **Abusing Account Operators Group** - Creating a new user
    
- **Abusing Account Operators Group** - Assigning a group to the newly created user
    
- **Abusing WriteDacl** in the domain - GrantingDCSync
    
- **DCSync Exploitation** - `secretsdump.py`
    

---

## 🔐 Walkthrough

### 🔍 Enumeration

### ⚔️ Exploitation

### 🚀 Privilege Escalation