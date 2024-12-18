## find open hosts
` for i in {1..254}; do ping -c 1 -W 1 X.X.X.$i > /dev/null && echo "X.X.X.$i is alive"; done`

## find open ports on those hosts
`for port in {1..65535}; do (echo >/dev/tcp/IP/$port) >/dev/null 2>&1 && echo "Port $port is open"; done`

## built-in network monitoring tool (windows 10)
* pktmon.exe

## Fping
* a - what is alive
* s - state
* g - generate target from cidr network
* q - not show results per target
* `fping -asgq [network]/[subnet]`