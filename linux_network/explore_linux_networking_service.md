# Explore the networking Service
## networking service
mainly by Debian servers:

- Verify if the networking service is running:

	`systemctl status networking.service`

	> Note: This is the main status command, but you could run other ones, such as:
	- `service networking status`
	- `/etc/init.d/networking status`

- Manipulate the service:
    - `systemctl stop networking`
    - `systemctl start networking`
    - `systemctl disable networking`
    - `systemctl enable networking`

- Disable/Enable and stop/start the service with one command:
	- `systemctl --now disable networking`
	- `systemctl --now enable networking`

### View the Network Configuration File
It is located at: /etc/networking/interfaces    

> Example:
    This example shows a static IP configuration.

    ```
    # The loopback network interface
    auto lo
    iface lo inet loopback

    # The primary network interface
    allow-hotplug enp1s0
    iface enp1s0 inet static
        address 10.0.2.51/24
        gateway 10.0.2.1
    ```

### Configuring DNS in Debian Server
- DNS configuration:

    `cat /etc/resolv.conf`
    
    > For example, my Debian server shows the DNS server IP as 10.0.2.1. That is the IP address of the built in DNS forwarder in the virtualization system (it is the same as the gateway address).     

change effect after Down and up the network interface (`ifdown` and `ifup`)

## Analyzing systemd-networkd in Ubuntu Server
The systemd-networkd service (or simply networkd) is used by Ubuntu server and in some other special situations.

- Check the service first:

    `systemctl status systemd-networkd`

### Using Netplan to set up a Static IP Configuration
Netplan is the front-end utility that is used to configure TCP/IP for Ubuntu Servers. The configuration files are written in YAML.

For example: `vim /etc/netplan/00-installer-config.yaml`

> Example:
```yaml
network:
  ethernets:
    ens3:
      addresses:
        - 10.0.2.53/24
      routes:
        - to: default
          via: 10.0.2.1
      nameservers:
        addresses:
          - 10.0.2.1
  version: 2
  renderer: networkd
```

- after change:
    - You can have netplan check your configuration by typing `netplan try`. 
    - If there are no error messages, proceed by saving the configuration: `netplan apply`

### Wireless IP 
a typical wireless configuration in Netplan will look like this:

```yaml
network:  
  wifis:
    wlp2s0b1:
      dhcp4: true
      access-points:
        "network_ssid_name":
          password: "**********"
```    
> Note: One of the security issues here is that the password for the WAP shows up in plain text. 

### Additional networkd-based Commands
- Use the `networkctl status` command to see the status of networkd-based interfaces. Here's an example of the results of that command.

```bash
root@ubuntu-server:~# networkctl status
           State: routable                         
         Address: 10.0.2.53 on enp1s0              
                  fe80::5054:ff:febc:b314 on enp1s0
         Gateway: 10.0.2.1 on enp1s0               
             DNS: 10.0.2.1                         
  Search Domains: example.com                      

Mar 30 14:41:36 ubuntu-server systemd[1]: Starting Network Service...
Mar 30 14:41:36 ubuntu-server systemd-networkd[682]: Enumeration completed
Mar 30 14:41:36 ubuntu-server systemd[1]: Started Network Service.
Mar 30 14:41:36 ubuntu-server systemd-networkd[682]: enp1s0: IPv6 successfully enabled
Mar 30 14:41:36 ubuntu-server systemd-networkd[682]: enp1s0: Link UP
Mar 30 14:41:36 ubuntu-server systemd[1]: Starting Wait for Network to be Configured...
Mar 30 14:41:36 ubuntu-server systemd-networkd[682]: enp1s0: Gained carrier
Mar 30 14:41:37 ubuntu-server systemd-networkd[682]: enp1s0: Gained IPv6LL
Mar 30 14:41:37 ubuntu-server systemd[1]: Finished Wait for Network to be Configured.
```

## Analyzing the NetworkManager Service
The NetworkManager service is probably the most commonly used networking service in Linux. It is the default on RHEL/Fedora/CentOS, and is used by default on Debian (as a client), Ubuntu Desktop, Mint, and many more Linux distributions. 

- View the status of the service:

	`systemctl status NetworkManager`

There are several ways to modify the networking configuration on Linux systems that use NetworkManager. its **not** recommend config in configuration's file.

- The configuration files are stored in two locations:
    -  /etc/NetworkManager/system-connections  	(keyfile method - preferred)
    -  /etc/sysconfig/network-scripts						(older ifcfg method)

- **nmcli** - This is a popular tool because of its depth, and the fact that you can use it on servers and clients because it runs in the command line only.

### Viewing the NetworkManager Log
Linux keeps a "journal" or a list of events that have happened on the system in `journalctl` .

- Filter by the unit or service. Issue the following command:

	`journalctl -u NetworkManager`

	This displays the journal entries related to the NetworkManager service. -u allows us to select the service (or unit).