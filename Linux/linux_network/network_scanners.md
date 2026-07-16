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

# net cat (nc)

## Command Line Options

|option | Description 
|-------|------------
|-h	    | help screen
|-u     | UDP mode
|-l	    | listen for incoming connections, which makes it a server process.
|-i seconds	 | The -i option defines the delay interval used by nc when sending lines or scanning ports.
|-v     | verbose output.
|-z	    | scan-only mode (no data transfer)
|-r	    | The -r option tells nc to use random local and remote ports, which might be good for testing.
|-o file	| The -o option tells nc to save the hex dump of network traffic to file, which might be handy for debugging.
|-n	    | The -n option tells nc to use IP addresses (numeric) only.
|-p port    | The -p option tells nc which port number to use.
|-b	    | The -b option tells nc to allow UDP broadcasts.
|-C	    | The -C option tells nc to send CRLF as line-ending.
|-T type    | The -T option allows nc to set the type of the TOS (Type Of Service) flag.
|-g gateway	| The -g option allows you to specify the route that the packets will take through the network. You can learn more about Source Routing here.
|-s address	| The -s option allows you to specify the local source address that will be used in the nc command.
|-t	    | The -t option is used for enabling telnet negotiation.
|-w timeout | timout in second

## usage
```bash
# Connect to server/localhost with 22 port
nc -v localhost 22

# udp connect
nc –v –u 8.8.8.8 53

# Port scanning
netcat -z -v domain.com 1-1000

# Check multiple ports
nc -zv google.com 80 443 8080
```

```bash
# Send a test packet and see what happens
echo "TEST" | nc -v -w 2 8.8.8.8 53

# If you get "Connection refused" → Firewall is explicitly rejecting
# If you get "Timeout" → Firewall is silently dropping (stealth mode)
# If you get "Connected" → Port is open and reachable
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