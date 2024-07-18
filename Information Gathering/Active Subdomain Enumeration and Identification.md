# ID
 
Windows Default IIS Versions:

![Exported image](Exported%20image%2020240712140422-0.png)  

Web Server ID:  
curl -I "[http://${TARGET}](http://${TARGET})"
 
```
whatweb -a3 [https://${TARGET}](https://${TARGET})" -v  
-a - aggression level  
-v - verbose























```
 
Wappalyzer browser extension does the same thing.
 
WafW00f - test web app firewall  
-a to check all measures  
-i. for input file  
-p proxy
 
Aquatone - mass visualized http attack surfaces

![Exported image](Exported%20image%2020240712140422-1.png)  

# ENUM

Zone transfer is how secondary DNS servers receive information from primary DNS. Primary DNS should be configured to allow zone transfers from secondary but may be misconfigured.
 
Example:  
    [https://hackertarget.com/zone-transfer/](https://hackertarget.com/zone-transfer/) and **zonetransfer.me** domain  
Manual Method:

1. Get name servers with dig ns
2. dig axfr zonetransfer.me @DNS OR

nslookup-type=any -query=AXFR zonetransfer.me nsztm1.digi.ninja
  
GOBUSTER:  
create a patterns text file like:

![Exported image](Exported%20image%2020240712140422-2.png)

gobuster dns -q -r "${NS}" -d "${TARGET}" -w "${WORDLIST}" -p ./patterns.txt -o "gobuster_${TARGET}.txt"  
dns - self  
-q - don’t print banner  
-r - use custom dns server  
-d - a target domain  
-p - path to patterns file  
-w - path to wordlist  
-o - output file