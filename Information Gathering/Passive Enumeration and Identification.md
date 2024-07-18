[Netcraft](https://www.netcraft.com/) - Background, network, hosting history info  
[Wayback Machine](http://web.archive.org/) - Find old versions of websites  
[waybackurls](https://github.com/tomnomnom/waybackurls) - inspect url's saved to the wayback machine  
go install github.com/tomnomnom/waybackurls@latest
 
==Certificates==  
==Another interesting source of information we can use to extract subdomains is SSL/TLS certificates. The main reason is Certificate Transparency (CT), a project that requires every SSL/TLS certificate issued by a Certificate Authority (CA) to be published in a publicly accessible log.==  
==We will learn how to examine CT logs to discover additional domain names and subdomains for a target organization using two primary resources:==

- ==https://censys.io==
- ==https://crt.sh==

==We can navigate to== ==https://search.censys.io/certificates== ==or== ==https://crt.sh== ==and introduce the domain name of our target organization to start discovering new subdomains.==