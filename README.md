# IT Help Desk Home Lab
This is my hands-on practice lab for IT support work. I'm building it project by project on a VirtualBox setup at home, and each project is written up with the steps I followed, screenshots, and what I learned from it.
I'm working towards my first IT support role. Before this I spent years in operations and health and safety jobs where following a process and writing things down properly was part of the work, and I'm bringing that habit into IT.

## The lab
The lab runs on VirtualBox. It has a Windows Server 2022 domain controller (DCG-DC01) for the `corp.local` domain and a Windows 10 workstation (DCG-WS01) joined to it. Most of the projects build on this same domain, so later ones reuse the users, groups and policies from the earlier ones, the way a real environment grows over time.

## Projects
I'm working through ten projects, roughly in the order a new support person would meet them on the job. The finished ones link to their write-ups.

| # | Project | Status |
|---|---------|--------|
| 1 | [Active Directory domain with users and Group Policy](active-directory-group-policy) | Done |
| 2 | Account lockouts, password resets, onboarding and offboarding | Up next |
| 3 | Ticketing system setup and working a ticket queue | Coming soon |
| 4 | Windows troubleshooting lab | Coming soon |
| 5 | Networking basics: DHCP, DNS and a small router setup | Coming soon |
| 6 | Remote support with RDP, Quick Assist and PowerShell remoting | Coming soon |
| 7 | PowerShell scripts for common admin tasks | Coming soon |
| 8 | Microsoft 365 and Entra ID tenant | Coming soon |
| 9 | Linux basics with a file share and print server | Coming soon |
| 10 | Backup, recovery and imaging | Coming soon |


## How each write-up is laid out
Each project has its own folder with a README and an images folder. The README covers what I wanted to achieve, what I built, how I tested it, and what I took from it. I keep the screenshots next to the text so you can see what I saw at each stage.

## About me
I'm Jayesh Dalvi, and I'm moving into IT support. If you want to talk about the projects or about roles, you can find me here:
- LinkedIn: linkedin.com/in/jayesh-dalvi-a114b079
- GitHub: github.com/jayesh-dalvi
