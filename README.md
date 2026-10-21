# Project 1: Active Directory Domain with Users and Group Policy

This is the first project in my IT help desk home lab series. I set up a structured Active Directory environment, organised users and groups the way a small company would, and used Group Policy to control what Sales staff can do and see on their workstations. Everything here is built on the same VirtualBox lab I use for my [SOC home lab](https://github.com/jayesh-dalvi/SOC_LAB).

## Goal

Most help desk tickets come back to Active Directory in some way: a user can't log in, a drive is missing, a setting is locked down. I wanted to understand how the pieces fit together, so I built a clean directory structure, tied access to security groups instead of individual users, and pushed settings out with Group Policy. I also wanted to prove it worked by testing it as different users.

## Lab environment

The domain is `corp.local`, running on a Windows Server 2022 domain controller called DCG-DC01. The client is a Windows 10 workstation called DCG-WS01, joined to the domain. Both are VirtualBox VMs on the same internal network, and the workstation uses the domain controller as its DNS server.

## What I did

### 1. Checked the domain and the client

Before changing anything I confirmed the baseline. On the domain controller, `Get-ADDomain` showed the `corp.local` domain with DCG-DC01 holding all the roles.

![Get-ADDomain output](images/01-domain-check.png)

On the workstation, `systeminfo` confirmed it was joined to `corp.local`, and `nslookup corp.local` returned the domain controller's address. If DNS is wrong, domain logins and Group Policy fail, so this is always the first thing worth checking.

![Workstation domain and DNS check](images/02-workstation-domain-and-dns.png)

### 2. Built the OU structure

In Active Directory Users and Computers I organised the directory into separate OUs for Admin, Computers, Groups, Service Accounts and Users. Under the Users OU I made a child OU for each department: Finance, HR, IT, Sales and SOC, plus a Disabled Users OU for accounts that have left the business.

The reason for splitting things this way is that Group Policy can only be linked to an OU, not to the default Users and Computers containers. A clean structure means I can apply different settings to different departments without them affecting each other.

![OU structure in Active Directory Users and Computers](images/03-ou-structure.png)

### 3. Created users and security groups

I created test users in each department OU and put them in the right place, for example Arnavi Dalvi in the Sales OU. I then created a security group for each department and added the users to it. For Sales the group is `Sales_Users_GG`. Giving access to a group rather than to named people means a new starter only needs to be added to one group to get everything they need.

I also moved the workstation's computer object out of the default Computers container and into the Computers OU, so it sits in a place where policies can be applied properly.

### 4. Set up a Sales file share

On the domain controller I created a folder at `C:\Shares\Sales` and shared it as `\\DCG-DC01\Sales`. I gave access to the `Sales_Users_GG` group only, so that members of Sales can open the folder and everyone else cannot.

### 5. Created and linked two Group Policy Objects

In Group Policy Management I created two GPOs and linked both to the Sales OU, so that they only affect users in that OU.

The first is `Sales-User-Restrictions`. It sits under User Configuration, Policies, Administrative Templates, Control Panel, and enables "Prohibit access to Control Panel and PC settings".

The second is `Sales-Drive-Map`. It uses Group Policy Preferences, under User Configuration, Preferences, Windows Settings, Drive Maps. It creates an S: drive that points to `\\DCG-DC01\Sales`.

![Both GPOs linked to the Sales OU](images/04-gpos-linked-to-sales-ou.png)

![Drive map settings in the policy editor](images/05-drive-map-setting.png)

## How I tested it

Both policies are user policies, so they apply at logon. On the workstation I signed in as a Sales user, ran `gpupdate /force`, signed out and back in, and then checked the results.

```
gpupdate /force
gpresult /r
```

The `gpresult /r` output shows both `Sales-User-Restrictions` and `Sales-Drive-Map` under the applied Group Policy Objects for the Sales user, and `Sales_Users_GG` in the list of security groups the user belongs to. It also shows the user's account sitting in `OU=Sales,OU=User(s)`, which is why the policies reach her.

![gpresult output for the Sales user](images/06-gpresult-sales-user.png)

The S: drive appears under Network locations in File Explorer, connected to `\\DCG-DC01\Sales`.

![S: drive mapped for the Sales user](images/07-s-drive-mapped.png)

To check that the settings were scoped properly, I then signed in as a user from a different department (HR). Trying to open `\\DCG-DC01\Sales` returns an access denied message, which is exactly what should happen because HR isn't in the Sales group.

![HR user denied access to the Sales share](images/08-hr-user-access-denied.png)

The HR user also has no S: drive, and Control Panel opens normally, which shows that the Sales policies only apply to the Sales OU.

![Control Panel opens normally for the HR user](images/09-hr-user-control-panel-open.png)

## What I took away from this

Group Policy depends on where objects sit in the directory, so the OU structure matters from the start. Policy settings follow the user or the computer depending on whether they are user or computer settings, and the logon token only picks up group membership at sign-in, so a change to a user's groups needs a fresh logon before it shows up. The `gpresult /r` command is the quickest way to see which policies actually reached a user and which groups they belong to, and I'll use it first whenever someone says "my drive is missing" or "a setting isn't applying". I also learned why you grant access to a group instead of to individuals, because it keeps permissions easy to manage and easy to audit.

## Commands used

```
Get-ADDomain
systeminfo | findstr /i domain
nslookup corp.local
gpupdate /force
gpresult /r
```

## Next

Project 2 covers the daily support tasks on this same domain: account lockouts, password resets, and onboarding and offboarding new starters.
