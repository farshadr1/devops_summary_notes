# Network Connectivity Troubleshooting Cheat Sheet

| Step | Command                | Purpose   |
| ---- | ---------------------- | --------- |
| 1    | `ping 127.0.0.1`       | Verify the local TCP/IP stack.|
| 2    | `ip addr` / `ip link`  | Verify the network interface is **UP** and has an IP address.|
| 3    | `ping <your-ip>`       | Verify the NIC driver and local IP configuration.|
| 4    | `ip -s link`           | Check RX/TX errors, dropped packets, carrier errors, duplex issues, bad cable.|
| 5    | `ip route`             | Verify the routing table and default gateway.|
| 6    | `ping <gateway-ip>`    | Verify connectivity to the router/LAN.|
| 7    | `nmap -sn <subnet>/24` or `ip neigh`  | Verify Layer 2 connectivity and discover local hosts.|
| 8    | `ping 8.8.8.8`         | Verify Internet connectivity (without DNS).|
| 9    | `dig google.com` / `nslookup google.com` / `ping google.com` | Verify DNS resolution.|
| 10   | `tracepath google.com` / `traceroute google.com`             | Identify routing problems and failed hops.|
| 11   | `ss -tulpn`            | View listening ports and active TCP connections.|
| 12   | `nc -vz <host> <port>` | Test whether a specific TCP port is reachable.|
| 13   | `sudo tcpdump -i <iface> -w capture.pcap` | Capture packets for analysis with Wireshark, or read directly with `sudo tcpdump -r capture.pcap`|

---

## Troubleshooting Flow

```text
127.0.0.1
      │
      ▼
Network Interface (UP + IP)
      │
      ▼
Own IP Address
      │
      ▼
Routing Table
      │
      ▼
Default Gateway
      │
      ▼
Other LAN Hosts
      │
      ▼
Public IP (8.8.8.8)
      │
      ▼
DNS Resolution
      │
      ▼
Application Port (nc)
      │
      ▼
Packet Capture (tcpdump)
```

---

## Common Problem Indicators

| Symptom                                 | Possible Cause                                  |
| --------------------------------------- | ----------------------------------------------- |
| Can't ping `127.0.0.1`                  | TCP/IP stack or OS networking problem           |
| Interface is DOWN                       | NIC disabled or driver issue                    |
| No IP address                           | DHCP/static IP configuration problem            |
| RX/TX errors increasing                 | Bad cable, faulty NIC, duplex mismatch          |
| Can't reach gateway                     | Switch, VLAN, subnet mask, cable, gateway issue |
| Can ping gateway but not `8.8.8.8`      | Routing or ISP problem                          |
| Can ping `8.8.8.8` but not `google.com` | DNS problem                                     |
| `nc` connection refused                 | Service not listening or blocked                |
| `nc` connection timed out               | Firewall, routing, or host unreachable          |
| ARP entry is `FAILED`                   | Layer 2 connectivity problem                    |
| TCP retransmissions in `tcpdump`        | Packet loss or network congestion               |
