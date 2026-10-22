# Active Directory Home Lab (Apple M2 + UTM)

I'm building a Windows Server domain controller from scratch on a MacBook and documenting every step, including the things that broke. I'm an IT student new to Active Directory, so this repo is a learning log as much as a finished project.

**Status:** In progress. DC01 is installed, renamed, and has a static IP. Next up is installing AD DS and promoting it to a domain controller for `corp.local`.

## What is Active Directory and why build this?

Active Directory (AD) is Microsoft's centralized directory for a network. I think of it as a digital phonebook and a security guard: it keeps track of users, computers, printers, groups and shared folders, and controls who can access what.

Why it matters to a business:
- One account works on any domain-joined PC
- Identity is managed in one place
- One change can apply to every machine (Group Policy)
- Offboarding someone means disabling one account

## Environment

| Item | Detail |
|---|---|
| Host | MacBook, Apple M2, 16 GB RAM |
| Hypervisor | UTM |
| Network | UTM Shared Network, `192.168.64.0/24` (gateway and DHCP at `192.168.64.1`) |
| Server OS | Windows Server 2022 Standard Evaluation (Desktop Experience), 180-day evaluation |
| Client OS | Windows 11 ARM64 |

Note: Microsoft's Evaluation Center only offers Windows Server 2022 as x64, and my Mac is ARM. DC01 runs in UTM's **Emulate** mode (x86_64), which works but is slower than a native ARM VM. ARM64 Server builds exist only as unofficial builds. See [troubleshooting](docs/troubleshooting.md) for how I got here.
## Lab layout

| Machine | OS | Role | Specs | Status |
|---|---|---|---|---|
| DC01 | Windows Server 2022 | Domain controller + DNS | 2 cores, 4 GiB RAM, 60 GB disk | Installed, static IP `192.168.64.10/24` |
| CLIENT01 | Windows 11 ARM64 | Workstation that joins `corp.local` | 2 cores, 4 GB RAM, 60 GB disk | Planned (ISO downloaded) |

DC01 settings: gateway `192.168.64.1`, DNS `127.0.0.1` (points at itself until AD DNS is set up). CLIENT01's DNS will point at DC01.

**Restore points** (full clones in UTM, never started):
- `DC01-clean-install`: Windows Server right after install, nothing changed
- `DC01-static-ip`: renamed and static IP set, before installing AD DS

## Progress

- [x] Research: what AD is and how it works
- [x] Pick a hypervisor that works on Apple Silicon (UTM)
- [x] Install Windows Server 2022 in DC01
- [x] Clean-install backup
- [x] Rename to DC01 and set static IP
- [x] Restore point before AD DS
- [ ] Install the AD DS role
- [ ] Promote DC01 to a domain controller (new forest `corp.local`)
- [ ] Create OUs, users and groups
- [ ] Build CLIENT01 and join the domain
- [ ] Group Policy
- [ ] Network diagram (Cisco Packet Tracer)
- [ ] Later ideas: Splunk server, a machine for attack testing

## What's in this repo

- [`docs/journal.md`](docs/journal.md): dated lab journal (Observation, Change, Next step)
- [`docs/troubleshooting.md`](docs/troubleshooting.md): problems I hit and how I fixed them
- `screenshots/`: proof of each step
- `diagrams/`: network diagram (coming)

## What I've learned so far

- Check one thing at a time. On attempt 3 I changed two settings together and fixed the install, but I can't say which one did it.
- Take a restore point before every risky step. Cloning the VM saved me from redoing a multi-hour install.
- Write down failures. The errors taught me more than the steps that worked.

## Resources

- [Active Directory Project (Home Lab) | Intro](https://www.youtube.com/watch?v=5OessbOgyEo)
- [Active Directory From Zero: The Homelab Build That Gets You Hired](https://www.youtube.com/watch?v=3pP0SaSrgSU&list=PLpO81gaJd_ANxwmyhiOctVh6QdxV-DneF)
- [Microsoft: AD DS overview](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)
- [UTM for Mac](https://mac.getutm.app/)
- - [Broadcom: Arm guests in Fusion on Apple silicon](https://knowledge.broadcom.com/external/article/315602/compatibility-considerations-for-arm-gue.html)

Lab passwords are not stored in this repo. All IP and MAC addresses here are private lab values.
