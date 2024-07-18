> [!caution] This page contained a drawing which was not converted.   

**Ports: 161, 162 (trap) UDP**
 
**SNMPv1 -**  
No auth  
No encryption - plain text
 
**SNMPv2 -**  
"Community string"* has no built in authentication
 
**SNMPv3 -**  
Username/password as well as transmission encryption via PSK  
More config options
 
**Community strings* -**  
Like a password that is used to determine if requested info can be viewed. In SNMPv2, everytime data is sent over network the CS can be intercepted.
 
**Dangerous Settings -**

![Exported image](Exported%20image%2020240712140404-0.png)  

**Footprinting -**

|   |   |
|---|---|
|snmpwalk|query OIDs with their information|
|onesixtyone|brute-force the community strings|
|braa|once CS is known, brute-force OIDS|

![Exported image](Exported%20image%2020240712140404-1.png) ![Exported image](Exported%20image%2020240712140404-2.png)    ![Exported image](Exported%20image%2020240712140404-3.png)