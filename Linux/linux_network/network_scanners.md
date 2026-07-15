 # Nmap-Cheat-Sheet

```bash
# Default scan
nmap 192.168.1.10

# Scan entire subnet
nmap 192.168.1.0/24

# SYN (Stealth) scan
nmap -sS 192.168.1.10

# UDP scan
nmap -sU 192.168.1.10

# Scan specific ports
nmap -p 22,80,443 192.168.1.10

# Scan all ports
nmap -p- 192.168.1.10

# Detect service and version
nmap -sV 192.168.1.10

# OS detection
nmap -O 192.168.1.10

# Aggressive scan. Enable OS + Version + NSE + Traceroute detection
nmap -A 192.168.1.10

# Ping sweep all live hosts
nmap -sn 192.168.1.0/24

# Top 1000 ports
nmap --top-ports 1000 192.168.1.10

# Save all output formats
nmap -oA scan_result 192.168.1.10
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