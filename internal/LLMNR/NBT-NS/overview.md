# what is it
* Link-Local Multicast Name Resolution (LLMNR)
    * UDP 5355
* NetBIOS Name Service (NBT-NS) broadcasts
    * UDP 137
* alternate methods if DNS fails


# Tools
- responder, inveigh


# How to Fix
## LLMNR
* Group Policy
    * Computer Configuration --> Administrative Templates --> Network --> DNS Client and enabling "Turn OFF Multicast Name Resolution."

## NBT-NS
* have to disable locally on each machine
    - Network and Sharing Center under Control Panel
    - change adapter settings > properties
    - selecting Internet Protocol Version 4 (TCP/IPv4) > Properties button
    - Advanced 
    - WINS tab > Disable NetBIOS over TCP/IP.
* deploy a script like so
```powershell
$regkey = "HKLM:SYSTEM\CurrentControlSet\services\NetBT\Parameters\Interfaces"
Get-ChildItem $regkey |foreach { Set-ItemProperty -Path "$regkey\$($_.pschildname)" -Name NetbiosOptions -Value 2 -Verbose}
```
    - Local Group Policy Editor > Startup > PowerShell Scripts
    - "For this GPO, run scripts in the following order" (powershell first) 
    - Add our script
* Deploy via GPO (SYSVOL share) 

## Monitoring
* ports UDP 5355 and 137
* event IDs 4697 and 7045  
* HKLM\Software\Policies\Microsoft\Windows NT\DNSClient
    - EnableMulticast DWORD ( 0 means disabled )