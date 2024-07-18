Ports 111, 2049
 
Tools:  
nmap  
sudo nmap -sV -p111,2049 --script nfs* $ip -v
 
smbclient  
list shares - burray@htb[/htb]$ smbclient -N -L //10.129.14.128  
connect to share - burray@htb[/htb]$ smbclient //10.129.14.128/<share>
 
rpcclient  
i.e. burray@htb[/htb]$rpcclient -U ""10.129.14.128

![Exported image](Exported%20image%2020240712140359-0.png)

```
brute force user RIDs - for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done









```
 
impacket - samrdump.py  
i.e. burray@htb[/htb]$ samrdump.py 10.129.14.128
 
SMBmap  
i.e. burray@htb[/htb]$ smbmap -H 10.129.14.128
 
CrackMapExec  
i.e. burray@htb[/htb]$ crackmapexec smb 10.129.14.128 --shares -u '' -p ''
 
enum4linux-ng -enumeration  
i.e. burray@htb[/htb]$ ./enum4linux-ng.py 10.129.14.128 -A