---
machine: Pov
platform: HackTheBox
difficulty: Medium
os: Windows
ip: <10.10.x.x>
status: in-progress # or pwned
start_date: YYYY-MM-DD
end_date: YYYY-MM-DD
tags: [htb, windows, pov]
---

# Pov — Overview

## 🎯 Objective
- [ ] user.txt
- [ ] root.txt
- Quick notes on what I’m trying right now:
  - …

## 📌 30-second summary
**High-level idea of the box (1–3 lines):**
- e.g., Web + config leaks → execution → credentials → remote access → privesc.

## 🧭 Attack Path (kill chain)

1. **Enumeration** → [[01-Enumeration]]
2. **Web / Subdomain / Key endpoint** → [[02-Web-Subdomain-dev.pov.htb]]
3. **Foothold (main vector)** → [[03-Foothold-RemoteFileRead-web.config]] / [[04-Foothold-ASP.NET-ViewState-Deserialization]]
4. **Movement / credentials** → [[05-Lateral-connection.xml-PSCredential-DPAPI]]
5. **Stable access (WinRM/tunnel)** → [[06-Access-WinRM-Chisel-Proxychains]]
6. **PrivEsc** → [[07-PrivEsc-SeDebugPrivilege-PsGetSys]]
7. **Post** → [[08-PostExploitation-Loot-Flags-Notes]]

## 🧩 Services and attack surface
| Service | Port | Detail | Note |
|---|---:|---|---|
| HTTP | 80 | … | [[01-Enumeration]] |
| WinRM | 5985 | … | [[06-Access-WinRM-Chisel-Proxychains]] |
| … | … | … | … |

## 🌐 Hosts / DNS / Important paths
- Hosts:
  - `pov.htb`
  - `dev.pov.htb` (if applicable)
- Key URLs/paths:
  - `/…`
  - `/…`

## 🔐 Credentials / Secrets (controlled)
> Store **only an index** here and point the details to a separate “secrets” note if you want.
- `user: <redacted>` → source: `<file / endpoint>` → [[05-Lateral-connection.xml-PSCredential-DPAPI]]
- Hashes/keys: `<redacted>` → [[...]]
- Notes: change/rotate / where it was used.

## 🧷 Artifacts and interesting files
- `web.config` → why it matters: machineKey/conn strings → [[03-Foothold-RemoteFileRead-web.config]]
- `connection.xml` → why it matters: credentials → [[05-Lateral-connection.xml-PSCredential-DPAPI]]
- Other: …

## 🧱 Access stability
- Main method: (WinRM / reverse shell / etc.)
- Persistence (if you did any in HTB): what and where (no unnecessary details)
- Cleanup: what you removed / what you left behind

## 🏁 Flags / Loot
- user.txt: ✅ (path: `C:\Users\...\Desktop\user.txt`)
- root.txt: ✅ (path: `C:\Users\...\Desktop\root.txt`)
- Other proof: screenshots, relevant outputs.

## 🧠 Issues and quick fixes
- Issue: …
  - Fix: …
- Issue: …
  - Fix: …

## ✅ Final checklist
- [ ] Documented the full chain
- [ ] Saved key evidence (screenshots/outputs)
- [ ] Noted the final commands that worked (in the relevant note)
- [ ] Wrote “Lessons learned”

## 🧾 Lessons learned (3–6 bullets)
- What I’m taking away from this box:
  - …
  - …
