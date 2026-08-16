# Home Lab: Active Directory Domain Controller

Built and documented a virtualised enterprise network — a working Active Directory
domain controller running on Windows Server 2022, with an Ubuntu client, deployed
entirely in VirtualBox on a home PC.

## Environment

| Component | Spec |
|---|---|
| Host | AMD Ryzen 5 3600, 16GB RAM, RX 6600 XT |
| Hypervisor | Oracle VM VirtualBox |
| Domain Controller | Windows Server 2022 Standard |
| Client | Ubuntu Desktop 24.04 LTS |
| Domain | home.lab |
| Networking | NAT + Host-only |

## What I built

**Day 1 — Linux foundation.** Stood up an Ubuntu VM, verified networking
(static IP via NAT, internet connectivity), updated packages, and enabled SSH.
Full write-up: [day1-setup.md](day1-setup.md)

**Day 2 — Active Directory.** Installed the AD DS role on Windows Server,
promoted it to a domain controller for `home.lab`, confirmed DNS was
configured automatically, and created a domain user account.
Full write-up: [day2-active-directory.md](day2-active-directory.md)

![Domain controller desktop after promotion](screenshots/01-domain-controller-desktop.png)

![Active Directory Administrative Center](screenshots/02-ad-admin-center.png)

![Domain users under home.lab](screenshots/03-ad-users-domain.png)

## Troubleshooting

### Locked out of the domain controller (local Administrator password lost)

**Problem:** Forgot the local Administrator password for DC01 and couldn't log in.

**Fix — offline password reset via install media:**

1. Attached the Windows Server 2022 evaluation ISO and booted from it, then opened
   a command prompt at the Windows Setup screen (`Shift+F10`).
2. Used `diskpart` → `list volume` to confirm the real Windows partition. WinPE
   assigns its own drive letters, which don't match the installed OS's — the
   59GB NTFS partition (not the one labelled "System Reserved") was the actual
   OS drive.
3. Swapped `utilman.exe` for `cmd.exe` (Ease of Access replacement technique): move D:\windows\system32\utilman.exe D:\windows\system32\utilman.exe.bak copy D:\windows\system32\cmd.exe D:\windows\system32\utilman.exe
4. Rebooted into Windows normally, clicked the Ease of Access icon on the
   login screen — it launched a SYSTEM-level command prompt instead of the
   normal accessibility panel.
5. Reset the password from there: net user Administrator NewPassword
**Restoring `utilman.exe` afterward** took two failed attempts before working:

- First attempt used `copy /y`, which is `cmd.exe` syntax — PowerShell's
  `copy` alias (`Copy-Item`) doesn't take that flag.
- Second attempt used correct `Copy-Item` syntax but still failed, because the
  shell in use was **32-bit PowerShell (x86)**. On 64-bit Windows, 32-bit
  processes get silently redirected from `System32` to `SysWOW64` (WOW64 File
  System Redirection), so the process couldn't see the file actually sitting
  in the real `System32` folder.
- Fixed by opening the native 64-bit PowerShell and running: Copy-Item -Path C:\Windows\System32\utilman.exe.bak -Destination C:\Windows\System32\utilman.exe -Force
-  **Outcome:** Regained access, restored `utilman.exe` to its original state,
took a VM snapshot afterward to avoid repeating the process.

### No internet connectivity after reboot (misconfigured NIC binding)

**Problem:** `ping 8.8.8.8` failed with "General failure" — not a DNS issue,
a routing issue: no default gateway on either adapter.

**Diagnosis:** `ipconfig /all` showed both NICs sitting on the
`192.168.56.x` Host-only range. Checking VirtualBox's adapter settings
against each NIC's MAC address in Windows revealed the static
`192.168.56.10` IP had been assigned to the adapter physically wired to
**NAT** (MAC ending `4B-86`), while the actual **Host-only** adapter
(MAC ending `5A-B4`) was left on DHCP. The NAT adapter had a Host-only-style
static address, so it had no route out — and the Host-only adapter, having no
gateway of its own by design, couldn't provide one either.

**Fix:** Reconfigured the NAT-attached adapter ("Ethernet") to obtain an IP
automatically. It picked up `10.0.3.15` with gateway `10.0.3.2` via NAT.
Left the Host-only adapter ("Ethernet 2") on DHCP, which is correct for that
network type.

**Outcome:** `ping 8.8.8.8` succeeded — 4/4 packets, 0% loss, ~40ms round trip.

![Network troubleshooting - NIC binding fix confirmed](screenshots/04-network-troubleshooting.png)

## Skills demonstrated

- System administration (Windows Server, Active Directory Domain Services)
- Linux fundamentals (Ubuntu install, package management, SSH)
- Networking (NAT vs Host-only adapters, static/DHCP config, gateway routing, DNS)
- Identity and access management (domain user creation, AD Administrative Center)
- Windows internals (WOW64 file system redirection, offline password recovery)
- Systematic troubleshooting (isolating root cause before applying a fix)
- Technical documentation

## Next steps

- [ ] Join Ubuntu client to the domain
- [ ] Configure Group Policy
- [ ] Harden security (firewall rules, least-privilege accounts)
