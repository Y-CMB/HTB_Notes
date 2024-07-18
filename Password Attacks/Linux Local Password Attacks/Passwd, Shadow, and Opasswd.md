Most common linux authentication mechanism = [Pluggable Authentication Modules (PAM)](https://web.archive.org/web/20220622215926/http://www.linux-pam.org/Linux-PAM-html/Linux-PAM_SAG.html)
- pam_unix.so
- pam_unix2.so
*located at ` /usr/lib/x86_x64-linux-gnu/security`*

### Passwd File

| Login name |     | Password info |     | UID    |     | GUID   |     | Full name/comments |     | Home directory   |     | Shell       |
| ---------- | --- | ------------- | --- | ------ | --- | ------ | --- | ------------------ | --- | ---------------- | --- | ----------- |
| `cry0l1t3` | `:` | `x`           | `:` | `1000` | `:` | `1000` | `:` | `cry0l1t3,,,`      | `:` | `/home/cry0l1t3` | `:` | `/bin/bash` |
x (Password info) means /etc/shadow is used.
### Shadow File

| Username   |     | Encrypted password             |     | Last PW change |     | Min. PW age |     | Max. PW age |     | Warning period | Inactivity period | Expiration date | Unused |
| ---------- | --- | ------------------------------ | --- | -------------- | --- | ----------- | --- | ----------- | --- | -------------- | ----------------- | --------------- | ------ |
| `cry0l1t3` | `:` | `$6$wBRzy$...SNIP...x9cDWUxW1` | `:` | `18937`        | `:` | `0`         | `:` | `99999`     | `:` | `7`            | `:`               | `:`             | `:`    |
*If the password field contains ==!== or ==\*==, the user can't login with a Unix password*

==Encrypted Password==: -`$<type>$<salt>$<hashed>`
==Algorithm Types==:
- `$1$` – MD5
- `$2a$` – Blowfish
- `$2y$` – Eksblowfish
- `$5$` – SHA-256
- `$6$` – SHA-512

### OPasswd
PAM can prevent reusing old passwords. Old passwords are stored in `/etc/security/opasswd`. *Root is needed to read the file by default.*

## Cracking Linux Credentials
Once hashes are known - 

#### Hashcat - MD5
1. 
```shell-session
hashcat -m 500 -a 0 md5-hashes.list rockyou.txt
```

#### Unshadow
1. Unshadow (john)
```shell-session
unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes
```
2. Hashcat - Unshadowed
```shell-session
hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/unshadowed.cracked
```


