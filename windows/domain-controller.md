## Ridbrute from NXC
* `nxc smb $DC-ip -u "guest" -p "" --rid-brute`
* If you just want the users
* `cat users | grep SidTypeUser | awk '{print $6}' | awk -F\\ '{print $2}'`

## Enumerate for ldapdomaindump
* https://github.com/dirkjanm/ldapdomaindump
* `python3 ldapdomaindump.py ldap://$DC -u '$DOMANI\$USER' -p '$PASS'`