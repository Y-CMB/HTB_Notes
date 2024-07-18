# SSH - 22

- Can only allow access from certain clients
- SSH1 vuln to MITM

**Dangerous Settings -**
 ![Inline image OWAPstImg370217](Exported%20image%2020240712140408-0.png)

   
**Footprinting - ssh-audit**
 ![Inline image OWAPstImg52426](Exported%20image%2020240712140408-1.png)

   
Preferred auth -  
Step 1: specify -v for debug
 ![Inline image OWAPstImg812539](Exported%20image%2020240712140408-2.png)

Step 2: Use -o for preferred auth arg
 ![Inline image OWAPstImg195867](Exported%20image%2020240712140408-3.png)

Notice the banner version - 8.2p1 only accepts SSH-2 where 3.9p1 accepts both (MITM for initial auth)  
   
**Resync - port 873**  
Used for backups and mirroring using delta transfers. Can use ssh.  
How it can be abused [here](https://book.hacktricks.xyz/network-services-pentesting/873-pentesting-rsync).  
   
**Enumerating -**  
Nc -nv <IP> to look for open shares.
 ![Inline image OWAPstImg235329](Exported%20image%2020240712140408-4.png)

   
**R-Services**  
Ports 512, 513, 514 and only accessible from r-commands. Vulnerable to MITM due to unencrypted transmission.  
   
Commonly abused commands:
 ![Inline image OWAPstImg602044](Exported%20image%2020240712140408-5.png)

   
Using rlogin:
 ![Inline image OWAPstImg497634](Exported%20image%2020240712140408-6.png)

   
   
R-commands programs:

1. Rcp (remote copy)
2. Rexec (remote execution)
3. Rlogin (remote login)
4. Rsh (Remote shell)
5. Rstat
6. Runtime

Rwho (remote who)