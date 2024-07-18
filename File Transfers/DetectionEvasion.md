User-Agent Resources:  
[http://useragentstring.com/index.php](http://useragentstring.com/index.php)  
[http://useragentstring.com/pages/useragentstring.php](http://useragentstring.com/pages/useragentstring.php)
 
Powershell Invoke-WebRequest:  

```
PS C:\htb>[Microsoft.PowerShell.Commands.PSUserAgent].GetProperties() | Select-Object Name,@{label="User Agent";Expression={[Microsoft.PowerShell.Commands.PSUserAgent]::
$
(
$_
.Name
)}}
|
flName       : InternetExplorer
￼
User Agent : Mozilla/5.0 (compatible; MSIE 9.0; Windows NT; Windows NT 10.0; en-US)
￼

Name       : FireFox
￼
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) Gecko/20100401 Firefox/4.0
￼

Name       : Chrome
￼
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6 (KHTML, like Gecko) Chrome/7.0.500.0
￼
             Safari/534.6
￼

Name       : Opera
￼
User Agent : Opera/9.70 (Windows NT; Windows NT 10.0; en-US) Presto/2.2.1
￼

Name       : Safari
￼
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/533.16 (KHTML, like Gecko) Version/5.0
￼
             Safari/533.16


```

$($_.Name)}}|flName : InternetExplorer￼User Agent : Mozilla/5.0 (compatible; MSIE 9.0; Windows NT; Windows NT 10.0; en-US)￼  
Name : FireFox￼User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) Gecko/20100401 Firefox/4.0￼  
Name : Chrome￼User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6 (KHTML, like Gecko) Chrome/7.0.500.0￼ Safari/534.6￼  
Name : Opera￼User Agent : Opera/9.70 (Windows NT; Windows NT 10.0; en-US) Presto/2.2.1￼  
Name : Safari￼User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/533.16 (KHTML, like Gecko) Version/5.0￼ Safari/533.16
 
Make the Request:

![Exported image](Exported%20image%2020240712140351-0.png)  

PowerShell/NC may be blocked, consider LOLBINs like Intel Graphics Driver:

![Exported image](Exported%20image%2020240712140351-1.png)

[https://lolbas-project.github.io/](https://lolbas-project.github.io/)