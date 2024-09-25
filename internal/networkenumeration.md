## find open hosts
` for i in {1..254}; do ping -c 1 -W 1 X.X.X.$i > /dev/null && echo "X.X.X.$i is alive"; done`

## find open ports on those hosts
`for port in {1..65535}; do (echo >/dev/tcp/IP/$port) >/dev/null 2>&1 && echo "Port $port is open"; done`
