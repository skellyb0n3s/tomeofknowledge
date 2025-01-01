* takes advantage that pre-authentication does not generate alerts/logs
* use in conjunction with wordlist like jsmith and jsmith2 `https://github.com/insidetrust/statistically-likely-usernames`
* `kerbrute userenum -d [domain] --dc [dc] jsmith.txt -o users`

# Kerbrute
* how it works
    - does not generate Windows event ID 4625: An account failed to log on
    - works by requestnig TGT without Kerberos Pre-Authentication. If Server responds, account valid. But if it is valid, it will count towards the user's password count
* example
    - `kerbrute userenum -d windomain.local --dc [target]] /opt/jsmith.txt `
* how it can be detected
     - 4768: A Kerberos authentication ticket (TGT) was requested. 
     - Triggered if Kerberos event logging is enabled via Group Policy. 