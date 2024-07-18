**PORT 110, 143,** **993, 995 encrypted**
 
## IMAP -

- management of emails directly on server
- supports folder structures
 
It can be unencrypted but SSL/TLS is used on port **143** or **993**
 
**Commands:**

![Exported image](Exported%20image%2020240712140403-0.png)

**1 STATUS "INBOX" (MESSAGES) - tells you how many messages are in the inbox**  
**1 FETCH <ID> (BODY[])**

## POP3 -

- only functions are listing, retrieving and deleting emails
       
**Commands:**

![Exported image](Exported%20image%2020240712140403-1.png)

Enumeration -
 
curl:

![Exported image](Exported%20image%2020240712140403-2.png)  

openssl or ncat:

![Exported image](Exported%20image%2020240712140403-3.png) ![Exported image](Exported%20image%2020240712140403-4.png)