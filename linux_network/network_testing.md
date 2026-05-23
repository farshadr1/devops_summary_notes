# network testing and best practices

## ping for checking tcp/ip
- ping packet size and amount of replies

    `ping -c 5 -s 1024 10.0.2.1`

The maximum packet size on most versions of Linux is 65507 bytes, which creates fragmented information, and could be blocked by a security device, so the maximum recommended testing amount is around 1400 bytes(related to mtu paramaeter). 

## system informations
- see hostname and operating system and some useful info:
        
    `hostnamectl`

        ```
        Static hostname: rv-B53J
        Icon name: computer-laptop
        Chassis: laptop 💻
        Machine ID: a9015e743e7e40788f48dde74563e4b2
        Boot ID: 29684872a5af4afb835ea061c006fa8f
        Operating System: Linux Mint 22.1                 
        Kernel: Linux 6.8.0-111-generic
        Architecture: x86-64
        Hardware Vendor: ASUSTeK Computer Inc.
        Hardware Model: B53J
        Firmware Version: 207
        Firmware Date: Fri 2010-08-06
        Firmware Age: 15y 9month 1w 6d   
        ```
- change hostname:
    `hostnamectl set-hostname <newHostName>`

## DNS
Two main locations for DNS:

```bash
cd /etc/resolv.conf
cd /etc/systemd/resolve.conf
```

```bash
# for NetworkManager service use :
nmcli connection modify enp1s0 ipv4.dns 1.1.1.1

# temporarily tool for networkd-systemd:
resolvectl dns eth0 8.8.8.8 1.1.1.1
resolvectl status
```

### adding local resovle names:
`vim /etc/hosts`

## trace route
The `traceroute` or `tracepath` command is used to trace the path that packets take from your computer to a destination host across a network. It shows each hop (router) along the path and measures the time it takes to reach each hop.   

### Installation

```bash
# Ubuntu/Debian
apt install traceroute

# Check if installed
which traceroute
```

### Basic Usage

1. **Trace route to a website**

```bash
traceroute google.com
traceroute 8.8.8.8
```

### Options

Some popular option flags include:

```
-n          Don't resolve hostnames (show IP addresses only)
-w [sec]    Set timeout for responses (default 5 seconds)
-q [num]    Set number of probe packets per hop (default 3)
-m [hops]   Set maximum number of hops (default 30)
-p [port]   Set destination port (default 33434)
-f [ttl]    Set first TTL value (starting hop)
-g [addr]   Use loose source route gateway
-I          Use ICMP ECHO instead of UDP
-T          Use TCP SYN instead of UDP
-U          Use UDP (default)
-4          Force IPv4
-6          Force IPv6
-s [addr]   Set source address
-i [iface]  Set network interface
```

### mtr (Better alternative)
mtr combines the functionality of the traceroute and ping programs in a single network diagnostic tool.

```bash
mtr --report google.com
```

## Geographic and owners Analysis
```bash
# Use whois to identify hop locations
whois 203.0.113.1

# Use whois to identify domains
whois github.com -H -I
```

## dig - nslookup utility
use dig to troubleshoot DNS problems
```bash
# Querying the Domain Name
dig example.com
# Reverse pickup
dig -x <ip_address>
```

> note: : old command is `nslookup`

## SS - socket statistics
```bash
# All udp and tcp connections
ss -aut

# All udp and tcp connections with don't resolve service names
ss -aunt

## All liscening udp/tcp with pid
ss -plunt
```
> note: : old school command is `netstat`

## nmap - a network scanner
Nmap is used to discover hosts and services on a network by sending packets and analyzing the responses. see [nmap Doc](./network_scanners.md)

## nmcli - NetworkManager CLI tool
**nmcli** is the NetworkManager command-line interface.This is a very commonly used command line tool for analyzing, modifying, and troubleshooting network connections.

```bash
# Display the network configurations with DNS server
nmcli

# History of all connections
nmcli connection show
nmcli con show
nmcli c s
``` 

### Add an IP address with nmcli
Example:

In this example, we specify that the address to be added will be static, then we add the IP address 10.0.2.152/24, and then we add the gateway and DNS IP addresses. You can add multiple IPs if you needed to in this manner, just by comma separating them. 

```bash
nmcli connection modify "Wired connection 1" ipv4.method manual ipv4.address 10.0.2.152/24 ipv4.gateway 10.0.2.1 ipv4.dns 10.0.2.1

# to take effect:
nmcli connection down "Wired connection 1"
nmcli connection up "Wired connection 1"
```

Example:

By selecting "auto" we set the interface to obtain all TCP/IP information from a DHCP server (if one is available) including it's IP address, netmask, gateway address, and DNS server IP address.

```bash
nmcli c m "Wired connection 1" ipv4.method auto 
```
### other nmcli commands:
```bash
# Scan and connect Wifi interfaces
nmcli dev wifi

# Useful examples
man nmcli-examples
```

## other testing tools

### speedtest-cli

Test the download and upload speed.

`sudo apt install speedtest-cli`

### NetPerf

Network performance tools can be used to measure the data throughput between one system and another.

`sudo apt install NetPerf`
> it is in non-free repo

### btop

Geraphical version of top include current up/down speed.

`sudo apt install btop`