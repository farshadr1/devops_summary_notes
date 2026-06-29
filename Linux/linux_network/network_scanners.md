 # Nmap-Cheat-Sheet

- Nmap Target Selection
```bash
#Scan a single IP	               
nmap 192.168.1.1
#Scan a host	                     
nmap www.testhostname.com
#Scan a range of IPs	             
nmap 192.168.1.1-20
#Scan a subnet	                   
nmap 192.168.1.0/24
#Scan targets from a text file	   
nmap -iL list-of-ips.txt
```

- Nmap Port Selection
```bash
#Scan a single Port	                    
nmap -p 22 192.168.1.1
#Scan a range of ports                   
nmap -p 1-100 192.168.1.1
#Scan 100 most common ports (Fast)	      
nmap -F 192.168.1.1
#Scan all of 65535 ports	                  
nmap -p- 192.168.1.1
```
- Nmap Port Scan types
```bash
#Scan using TCP connect	                  
nmap -sT 192.168.1.1
#Scan using TCP SYN scan (default)	        
nmap -sS 192.168.1.1
#Scan UDP ports	                          
nmap -sU -p 123,161,162 192.168.1.1
#Scan selected ports - ignore discovery	  
nmap -Pn -F 192.168.1.1
```

- Service and OS Detection
```bash
#Detect OS and Services	           
nmap -A 192.168.1.1
#Standard service detection	       
nmap -sV 192.168.1.1
#More aggressive Service Detection	 
nmap -sV --version-intensity 5 192.168.1.1
#Lighter banner grabbing detection	 
nmap -sV --version-intensity 0 192.168.1.1
```

- Nmap Output Formats
```bash
#Save default output to file	       
nmap -oN outputfile.txt 192.168.1.1
#Save results as XML	               
nmap -oX outputfile.xml 192.168.1.1
#Save results in a format for grep	 
nmap -oG outputfile.txt 192.168.1.1
#Save in all formats	               
nmap -oA outputfile 192.168.1.1
```

- Digging deeper with NSE Scripts
```bash
#Scan using default safe scripts	   
nmap -sV -sC 192.168.1.1
#Get help for a script	             
nmap --script-help=ssl-heartbleed
#Scan using a specific NSE script	 
nmap -sV -p 443 –script=ssl-heartbleed.nse 192.168.1.1
#Scan with a set of scripts	       
nmap -sV --script=smb* 192.168.1.1
```

- A scan to search for DDOS reflection UDP services
```bash
#Scan for UDP DDOS reflectors	    
nmap –sU –A –PN –n –pU:19,53,123,161 –script=ntp-monlist,dns-recursion,snmp-sysdescr 192.168.1.0/24
```


- HTTP Service Information
```bash
#Gather page titles from HTTP services	   
nmap --script=http-title 192.168.1.0/24
#Get HTTP headers of web services	       
nmap --script=http-headers 192.168.1.0/24
#Find web apps from known paths	         
nmap --script=http-enum 192.168.1.0/24
```

- Detect Heartbleed SSL Vulnerability
```bash
#Heartbleed Testing	      
nmap -sV -p 443 --script=ssl-heartbleed 192.168.1.0/24
```

- IP Address information
```bash
#Find Information about IP address	
nmap --script=asn-query,whois,ip-geolocation-maxmind 192.168.1.0/24
```

- Scan port services from 1 to 65535
```bash
nmap -sV -p 1-65535 192.168.1.1/24
```

- All ports, all service versions, simple scripts = just the open
```bash
nmap -p- -sV -sC $IP --open
```

# Network Protocols & Standard Services
Protocol	| Port	                    | Notes
------------|---------------------------|------
SSH	        | 22                        | Secure shell access
FTP	        | 20 (data), 21 (control)	| File transfer
SFTP	    | 22	                    | SSH File Transfer Protocol
Telnet	    | 23                        | Remote terminal (insecure)
SMTP	    | 25	                    | Email sending (server-to-server)
SMTPS	    | 465	                    | SMTP with TLS
SMTP        | 587	                    | Authenticated email sending
POP3	    | 110	                    | Email retrieval (legacy)
POP3S	    | 995	                    | Secure POP3
IMAP	    | 143	                    | Email retrieval
IMAPS	    | 993	                    | Secure IMAP
DNS	        | 53 (UDP/TCP)	            | Domain name resolution
DHCP	    | 67 (server), 68 (client)	| Dynamic IP assignment
LDAP	    | 389	                    | Directory access
LDAPS	    | 636	                    | Secure LDAP
Kerberos	| 88	                    | Authentication
NTP	        | 123	                    | Time synchronization
SNMP	    | 161 (query), 162 (traps)	| Network monitoring
RDP	        | 3389	                    | Remote Desktop Protocol
VNC	        | 5900	                    | Remote desktop
Syslog	    | 514 (UDP)	                | Logging
SMB/CIFS	| 445	                    | Windows file sharing
NetBIOS	    | 137-139	                | Legacy Windows networking
NFS	        | 2049	                    | Network File System
RSYNC	    | 873	                    | File synchronization
Memcached	| 11211	                    | Distributed caching 

## Key Port Mappings

Port    | Service
--------|-------------
80	    | HTTP
443	    | HTTPS
3306	| MySQL
5432	| PostgreSQL
6379	| Redis
27017	| MongoDB
1433    | Microsoft SQL Server
1521    | Oracle Database
5000	| Flask
8000    | Django
3000    | Node.js, Grafana
4200    | Angular
8888    | Jupyter Notebook
9090    | Prometheus
10051   | Zabbix