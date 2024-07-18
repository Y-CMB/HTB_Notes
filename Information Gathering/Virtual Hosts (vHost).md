## ABOUT

feature that allows several websites to be hosted on a single server. two ways to configure…

1. IP-based virtual hosting
2. multiple network interfaces
    
    - multiple Ips on each interface can be configured
3. Name-based virtual hosting
4. The distinction for which domain the service was requested is made at the application level. For example, several domain names, such as admin.inlanefreight.htb and backup.inlanefreight.htb, can refer to the same IP. Internally on the server, these are separated and distinguished using different folders. Using this example, on a Linux server, the vHost admin.inlanefreight.htb could point to the folder /var/www/admin. For backup.inlanefreight.htb the folder name would then be adapted and could look something like /var/www/backup.
5. During our subdomain discovering activities, we have seen some subdomains having the same IP address that can either be virtual hosts or, in some cases, different servers sitting behind a proxy.  
**REMEMBER TO FUZZ**
 
curl -s -I [http://{IP}](http://{IP}) -H "Host: sub.domain.com" << will show response
 
## AUTOMATING THE FUZZ

**ffuf** - vHost Fuzzing

![Exported image](02%20-%20assets/Crawling%201.md/Exported%20image%2020240712140423-0.png)  

use -fs {size} to filter out default response  
ffuf -w ./vhosts -u [http://192.168.10.10](http://192.168.10.10) -H "HOST: FUZZ.randomtarget.com"-fs 612

- /usr/share/seclists/Discovery/dns instead of ./vhosts