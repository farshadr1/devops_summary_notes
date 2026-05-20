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

## trace route
The `traceroute` or `tracepath` command is used to trace the path that packets take from your computer to a destination host across a network. It shows each hop (router) along the path and measures the time it takes to reach each hop.   

### Installation

```bash
# Ubuntu/Debian
sudo apt install traceroute

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

## dig - DNS lookup utility
```bash
# querying the Domain Name
dig example.com
# reverse pickup
dig -x <ip_address>
```

> note: : old command is `nslookup`

## SS - socket statistics
```bash
# all udp and tcp connections
ss -aut

# all udp and tcp connections with don't resolve service names
ss -aunt

## all liscening udp/tcp with pid
ss -plunt
```
> note: : old command is `netstat`

## nmap - a network scanner
Nmap is used to discover hosts and services on a network by sending packets and analyzing the responses.[nmap Doc](linux_network/nmap.md)