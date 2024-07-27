Types:

| `tar`      | `gz`   | `rar`       | `zip`       |
| ---------- | ------ | ----------- | ----------- |
| `vmdb/vmx` | `cpt`  | `truecrypt` | `bitlocker` |
| `kdbx`     | `luks` | `deb`       | `7z`        |
| `pkg`      | `rpm`  | `war`       | `gzip`      |
Download an exhaustive list:
```shell-session
curl -s https://fileinfo.com/filetypes/compressed | html2text | awk '{print tolower($1)}' | grep "\." | tee -a compressed_ext.txt
```

### Cracking ZIP
Use zip2john:
`zip2john ZIP.zip > zip.hash`
`john --wordlist=rockyou.txt zip.hash`
`john zip.hash --show`

### Cracking OpenSSL Encryption
Identify:
```shell-session
wannacrye@htb[/htb]$ file GZIP.gzip 

GZIP.gzip: openssl enc'd data with salted password
```

Use a for loop to display extracted contents:
```shell-session
for i in $(cat rockyou.txt);do openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null| tar xz;done
```

Check again with `ls`

### Cracking BitLocker Encrypted Drives
*Using bitlocker2john will extract [four different hashes](https://openwall.info/wiki/john/OpenCL-BitLocker), simply switch the hashcat mode. The following is the first (bitlocker password)*

```shell
wannacrye@htb[/htb]$ bitlocker2john -i Backup.vhd > backup.hashes

wannacrye@htb[/htb]$ grep "bitlocker\$0" backup.hashes > backup.hash

wannacrye@htb[/htb]$ cat backup.hash

$bitlocker$0$16$02b329c0453b9273f2fc1b927443b5fe$1048576$12$00b0a67f961dd80103000000$60$d59f37e...SNIP...70696f7eab6b
```

Crack it with hashcat:
```shell-session
hashcat -m 22100 backup.hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt -o backup.cracked
```

View it:
```shell-session
cat backup.cracked 
```

Once we have cracked the password, ==mount== the encrypted drive in Windows. Then double-click and enter the password.
![[Pasted image 20240726192754.png]]

