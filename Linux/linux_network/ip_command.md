# Working with `ip` command

- Show all IP addresses:
    - `ip addr` or `ip a`
    - `ip -br -c a` option: --brief --color
    > note: old school command is `ifconfig` from net-tools package

- Add/Remove temproary IP address to interface:
    - `ip addr add/del 192.168.1.100/24 dev eth0`
    > note: old school command is

- Show all network interfaces(layer2):
    - `ip link` or `ip l`
    - Show interface statistics: `ip -s link` 

- Bring interface up/down:
    - `ip link set eth0 up/down`
    > note: do after changing ip

- Change MAC address:
    - `ip link set eth0 address 00:11:22:33:44:55`

- System information about interfaces:
    - `cd /sys/class/net/`

- Show routing table:
    - `ip route` or `ip r`
    > note: old school command is `route`

- Show routes for specific destination:
    - `ip route get 8.8.8.8`

- Remove gateway:
    - `ip r delete default`

- Add default gateway:
    - `ip r add default via 10.0.2.1`

- Add route via specific interface
    - `ip route add 172.16.0.0/16 dev eth1`

- Shows the current neighbour table in kernel
    - `ip neigh` or `ip n`