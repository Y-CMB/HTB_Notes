# Windows:

Use the Invoke-AESEncryption.ps1 script
 
## Examples:

```
.EXAMPLE
￼
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Text "Secret Text" 
￼

Description
￼
-----------
￼
Encrypts the string "Secret Test" and outputs a Base64 encoded ciphertext.
￼
 
￼
.EXAMPLE
￼
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Text "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs="
￼
 
￼
Description
￼
-----------
￼
Decrypts the Base64 encoded string "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs=" and outputs plain text.
￼
 
￼
.EXAMPLE
￼
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Path file.bin
￼
 
￼
Description
￼
-----------
￼
Encrypts the file "file.bin" and outputs an encrypted file "file.bin.aes"
￼
 
￼
.EXAMPLE
￼
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Path file.bin.aes

PS C:\htb> Import-Module .\Invoke-AESEncryption.ps1

PS C:\htb> Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt
￼

File encrypted to C:\htb\scan-results.txt.aes



burray@htb
[/htb]
$
openssl enc -aes256 -iter 
100000
-pbkdf2 -in /etc/passwd -out passwd.enc

enter aes-256-cbc encryption password:                                                         
￼
Verifying - enter aes-256-cbc encryption password:


burray@htb
[/htb]
$
openssl enc -d -aes256 -iter 
100000
-pbkdf2 -in passwd.enc -out 
passwd

enter aes-256-cbc decryption password:

```

￼Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Text "Secret Text" ￼  
Description￼-----------￼Encrypts the string "Secret Test" and outputs a Base64 encoded ciphertext.￼ ￼.EXAMPLE￼Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Text "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs="￼ ￼Description￼-----------￼Decrypts the Base64 encoded string "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs=" and outputs plain text.￼ ￼.EXAMPLE￼Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Path file.bin￼ ￼Description￼-----------￼Encrypts the file "file.bin" and outputs an encrypted file "file.bin.aes"￼ ￼.EXAMPLE￼Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Path file.bin.aes
 
## Practical:

PS C:\htb> Import-Module .\Invoke-AESEncryption.ps1  
PS C:\htb> Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt￼  
File encrypted to C:\htb\scan-results.txt.aes
 
# Linux:

OpenSSL is abundant on linux systems and provides many different ciphers.

- Specify -pbkdf2 (Password-Based Key Derivation Function 2 algorithm) if using a password
 
Encrypt:  
burray@htb[/htb]$openssl enc -aes256 -iter 100000-pbkdf2 -in /etc/passwd -out passwd.enc
 
enter aes-256-cbc encryption password: ￼Verifying - enter aes-256-cbc encryption password:
 
Decrypt:  
burray@htb[/htb]$openssl enc -d -aes256 -iter 100000-pbkdf2 -in passwd.enc -out passwd
 
enter aes-256-cbc decryption password: