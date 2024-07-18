> [!caution] This page contained a drawing which was not converted.   

Nginx - Enabling PUT

1. Create a Directory to Handle Upload Files
    
    1. ![Exported image](Exported%20image%2020240712140348-0.png)
2. Change owner to www-data
    
    1. ![Exported image](Exported%20image%2020240712140348-1.png)
3. Create Nginx Config File
    
    1. create /etc/nginx/sites-available/upload.conf
    2. server {￼ listen 9001;￼ ￼ location /SecretUploadDirectory/ {￼ root /var/www/uploads;￼ dav_methods PUT;￼ }￼}
4. Symlink our Site to sites-enabled Directory
    
    1. ![Exported image](Exported%20image%2020240712140348-2.png)
5. Start Nginx
    
    1. ![Exported image](Exported%20image%2020240712140348-3.png)
6. Check log if errors
    
    1. /var/log/nginx/error.log
7. Verify Errors
    
    1. ![Exported image](Exported%20image%2020240712140348-4.png)
      
    3. ![Exported image](Exported%20image%2020240712140348-5.png)
      
    5. ![Exported image](Exported%20image%2020240712140348-6.png)
8. To fix default nginx config listening on 80
    
    1. ![Exported image](Exported%20image%2020240712140348-7.png)
    
Now we can test by using cURL to send a PUT request…
 ![Exported image](Exported%20image%2020240712140348-8.png)  
![Exported image](Exported%20image%2020240712140348-9.png)