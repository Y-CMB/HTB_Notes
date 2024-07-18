![Exported image](02%20-%20assets/MySQL%201.md/Exported%20image%2020240712140405-0.png)  

Things to look for:

![Exported image](Exported%20image%2020240712140405-1.png)   
Footprinting:  
Listens on **port 1433**  
**NMAP**

![Exported image](Exported%20image%2020240712140405-2.png)

```
--script-args mssql.instance-port
=
1433
,mssql.username
=
sa,mssql.password
=
,mssql.instance-

name
=
MSSQLSERVER -sV -p 
143310.129
.201.248


```

=1433,mssql.username=sa,mssql.password=,mssql.instance-  
name=MSSQLSERVER -sV -p 143310.129.201.248  
**METASPLOIT**

![Exported image](Exported%20image%2020240712140405-3.png)

**mssqlclient.py (impacket)**

![Exported image](Exported%20image%2020240712140405-4.png)