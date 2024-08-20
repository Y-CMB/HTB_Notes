## Useful Commands

### Windows
##### CMD
Mount a share drive
```cmd-session
C:\htb> net use n: \\192.168.220.129\Finance
```

Mount a share drive with authentication
```cmd-session
C:\htb> net use n: \\192.168.220.129\Finance /user:plaintext Password123
```

Get how many files are in a directory
```cmd-session
C:\htb> dir n: /a-d /s /b | find /c ":\"
```

| **Syntax** | **Description**                                                |
| ---------- | -------------------------------------------------------------- |
| `dir`      | Application                                                    |
| `n:`       | Directory or drive to search                                   |
| `/a-d`     | `/a` is the attribute and `-d` means not directories           |
| `/s`       | Displays files in a specified directory and all subdirectories |
| `/b`       | Uses bare format (no heading information or summary)           |

Search within files for a string
```cmd-session
c:\htb>findstr /s /i cred n:\*.*
```

More [findstr here](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr#examples)


##### PowerShell
Get-Child item is the same thing as `dir`; display contents of directory
```powershell-session
PS C:\htb> Get-ChildItem \\192.168.220.129\Finance\
```

Instead of `net use`, `New-PSDrive` in PowerShell
```powershell-session
PS C:\htb> New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem"
```

PSCredential Object Example
```powershell-session
PS C:\htb> $username = 'plaintext'
PS C:\htb> $password = 'Password123'
PS C:\htb> $secpassword = ConvertTo-SecureString $password -AsPlainText -Force
PS C:\htb> $cred = New-Object System.Management.Automation.PSCredential $username, $secpassword
PS C:\htb> New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem" -Credential $cred
```

Find files in directory
```powershell-session
PS C:\htb> Get-ChildItem -Recurse -Path N:\ -Include *cred* -File
```
**Note** `-Include`

`Select-String` is like `grep` to search in files
```powershell-session
PS C:\htb> Get-ChildItem -Recurse -Path N:\ | Select-String "cred" -List
```

### Linux

Mount SMB shares
```shell-session
wannacrye@htb[/htb]$ sudo mkdir /mnt/Finance
wannacrye@htb[/htb]$ sudo mount -t cifs -o username=plaintext,password=Password123,domain=. //192.168.220.129/Finance /mnt/Finance
```

==or== use a credential file

```shell-session
wannacrye@htb[/htb]$ mount -t cifs //192.168.220.129/Finance /mnt/Finance -o credentials=/path/credentialfile
```
**`credentialfile` structure:**
==Name:==Code: txt
```txt
username=plaintext
password=Password123
domain=.
```
***sudo apt install cifs-utils***

Find command
```shell-session
wannacrye@htb[/htb]$ find /mnt/Finance/ -name *cred*
```

Search within files for `cred`
```shell-session
wannacrye@htb[/htb]$ grep -rn /mnt/Finance/ -ie cred
```

### Tools
  

| **SMB**                                                                                  | **FTP**                                     | **Email**                                          | **Databases**                                                                                                                |
| ---------------------------------------------------------------------------------------- | ------------------------------------------- | -------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| [smbclient](https://www.samba.org/samba/docs/current/man-html/smbclient.1.html)          | [ftp](https://linux.die.net/man/1/ftp)      | [Thunderbird](https://www.thunderbird.net/en-US/)  | [mssql-cli](https://github.com/dbcli/mssql-cli)                                                                              |
| [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec)                              | [lftp](https://lftp.yar.ru/)                | [Claws](https://www.claws-mail.org/)               | [mycli](https://github.com/dbcli/mycli)                                                                                      |
| [SMBMap](https://github.com/ShawnDEvans/smbmap)                                          | [ncftp](https://www.ncftp.com/)             | [Geary](https://wiki.gnome.org/Apps/Geary)         | [mssqlclient.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/mssqlclient.py)                             |
| [Impacket](https://github.com/SecureAuthCorp/impacket)                                   | [filezilla](https://filezilla-project.org/) | [MailSpring](https://getmailspring.com/)           | [dbeaver](https://github.com/dbeaver/dbeaver)                                                                                |
| [psexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py)   | [crossftp](http://www.crossftp.com/)        | [mutt](http://www.mutt.org/)                       | [MySQL Workbench](https://dev.mysql.com/downloads/workbench/)                                                                |
| [smbexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py) |                                             | [mailutils](https://mailutils.org/)                | [SQL Server Management Studio or SSMS](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms) |
|                                                                                          |                                             | [sendEmail](https://github.com/mogaal/sendemail)   |                                                                                                                              |
|                                                                                          |                                             | [swaks](http://www.jetmore.org/john/code/swaks/)   |                                                                                                                              |
|                                                                                          |                                             | [sendmail](https://en.wikipedia.org/wiki/Sendmail) |                                                                                                                              |