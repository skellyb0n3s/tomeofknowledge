# enum password policy
crackmapexec smb [ ] -u user -p pass --pass-policy

* smb null session -> typically due to misconfiguration legacy dc

# from linux
rpcclient -U "" -N [target]

> querydominfo

# enum4inux
enum4linux -P [target]
enum4linux-ng -P [target] -oA iltarget

# overview
nmblookup	137/UDP
nbtstat	137/UDP
net	139/TCP, 135/TCP, TCP and UDP 135 and 49152-65535
rpcclient	135/TCP
smbclient	445/TCP


# null session from windows
net use \\host\ipc$ "" /u:"" 

# guest account 
net use \\host\ipc$ "" /u:guest 


# ldap anonymous 
- server 2003, from then on authenticated users only
- specific tools (windapsearch.py, ldapsearch, ad-ldapdomaindump.py)
- using ldapsearch 
	- ` ldapsearch -h [target] -x -b "DC=target,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength`
	
	
# on windows host itself 
` net accounts`

# powerview
`Get-DomainPolicy`


# Default when new domain is created 

Policy	Default Value
Enforce password history	24 days
Maximum password age	42 days
Minimum password age	1 day
Minimum password length	7
Password must meet complexity requirements	Enabled
Store passwords using reversible encryption	Disabled
Account lockout duration	Not set
Account lockout threshold	0
Reset account lockout counter after	Not set


# Generating user Lists
`enum4linux -U [target]  | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]"`

* rpcclient
    - `enumdomusers`
* crackmapexec
    - `crackmapexec smb [target]] --users`
* ldapsearch
    - `ldapsearch -h [target] -x -b "DC=WINDOMAIN,DC=LOCAL" -s sub "(&(objectclass=user))"  | grep sAMAccountName: | cut -f2 -d" "`

* windapsearch
    - `./windapsearch.py --dc-ip [target] -u "" -U`

