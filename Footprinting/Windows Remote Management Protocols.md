1. Remote Desktop Protocol (RDP)
2. Windows Remote Management (WinRM)
3. Windows Management Instrumentation (WMI)

   
**RDP**  
Typically **port 3389**  
   
**Footprinting -**  
Nmap
 ![Inline image OWAPstImg329631](Exported%20image%2020240712140409-0.png)

Rdp-sec-check.pl  
Install…
 ![Inline image OWAPstImg972084](Exported%20image%2020240712140409-1.png)

Check…  

```
burray@htb[/htb]$ git clone 
https://github.com/CiscoCXSecurity/rdp-sec-check.git
&&
 cd rdp-sec-check

burray@htb[/htb]$ ./rdp-sec-check.pl 10.129.201.248


















burray@htb[/htb]$ /usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"

 

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

 

[*] SMBv3.0 dialect used

ILF-SQL-01
```

[https://github.com/CiscoCXSecurity/rdp-sec-check.git](https://github.com/CiscoCXSecurity/rdp-sec-check.git&&)&& cd rdp-sec-check  
burray@htb[/htb]$ ./rdp-sec-check.pl 10.129.201.248  
   
**Connect -**

1. Xfreerdp
2. Rdesktop
3. Remmina
 ![Inline image OWAPstImg837070](Exported%20image%2020240712140409-2.png)

   
**WinRM**  
**Ports 5985,** **5986 https**  
Windows 10 < must be explicitly configured but enabled by default Windows Server 2012 +  
   
**Footprinting -**  
Nmap:
 ![Inline image OWAPstImg456427](Exported%20image%2020240712140409-3.png)

Powershell:  
Test-WsMan <host>  
Evil-WinRM:
 ![Inline image OWAPstImg535440](Exported%20image%2020240712140409-4.png)

   
**WMI**  
Accessed by:

1. Powershell
2. VBScript
3. Windows Management Instrumentation Console (WMIC)

   
Footprinting -  
WMIexec.py (impacket)  
burray@htb[/htb]$ /usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"  
   
Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation  
   
[*] SMBv3.0 dialect used  
ILF-SQL-01