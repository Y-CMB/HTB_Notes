(Intelligent Platform Management Interface) **Port 623 UDP**
 
**Footprinting: nmap or metasploit (aux/scan/ipmi/)**

![Exported image](Exported%20image%2020240712140407-0.png) ![Exported image](Exported%20image%2020240712140407-1.png)

Default passwords -

![Exported image](Exported%20image%2020240712140407-2.png)  

**Dangerous settings:**  
If default creds don't work there is a flaw in the RAKP protocol in IPMI 2.0.  
 hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u  
To retrieve IPMI hashes, we can use the Metasploit ==IPMI 2.0 RAKP Remote SHA1 Password Hash Retrieval== module.
 
**Dumping hashes:**

![Exported image](Exported%20image%2020240712140407-3.png)