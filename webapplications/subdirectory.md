* Wfuzz
```
wfuzz -w /opt/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.website" --hc 403,400 --hl 337 -t 150 [ip]
```

* Fuff
```
ffuf -c -u 'http://url' -H 'host: FUZZ.website' -w wordlist here -fw [x] -fc 200 -mc all
```
