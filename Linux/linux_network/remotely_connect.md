# SSH connection

- If it is not installed: 
    `apt install openssh-server`

- Check if the OpenSSH server service is running.

	In most instances, OpenSSH uses two services: ssh (for the client) and sshd (for the server). We are interested in the server service on the target computer. To see if the server service is running, type the command:

	`systemctl status ssh`

    If it is not active and running, you can enable and start it by typing:

	`systemctl --now enable ssh`

    You can also check the "0.0.0.0:22" of the target system with the following command:

    `ss -aunt`

- > Note: If you are using VirtualBox for your virtual machines, better to set port forwarding. on NAT configuration menu, add Host:127.0.0.1:2222 and Guest:10.0.2.15:22


	`ssh user@127.0.0.1 -p 2222`

## Create an RSA-based key pair

This way, we don't have to send a user's password over the network, and instead rely on the much more secure SSH key process.

in client system:

* Configuring User Keys
	1. Generate a key on your client
		+ `ssh-keygen`
			> Dont use sudo, becuse it use for user login not root login.
		+ Keys are output to `~/.ssh`
		+ `id_rsa` is the private key
			> bigger keys for more security `ssh-keygen -b 4096`
		+ `id_rsa.pub` is the public key
		+ `id_rsa` is the private key
	2. Copy your public key from the client to the server
		+ `ssh-copy-id <username>@<server_name>`

in server system:
* Disable password authentication on the server
	1. `sudo vim /etc/ssh/sshd_config.d/hardened.conf`
	2. Set `PasswordAuthentication no`

Normally OpenSSH creates SSH host keys. Depending on the Linux distribution, they are generated during package installation.
but if we dont confidence vendor, generate ourselves.
1. Delete pre-existing keys
	- `sudo rm /etc/ssh/ssh_host*`
2. Generate every missing host key
	- `sudo ssh-keygen -A`

## Restricting Access to SSH 
* Limiting user access to SSH
	+ Modify the SSH config file in server
		- `sudo vim /etc/ssh/sshd_config.d/hardened.conf`
			```
			AllowUsers user1 user2 user3
			AllowGroups group1
			PermitRootLogin no
			```
* Limiting in ufw
	+ Limit by try
		- `sudo ufw limit ssh/tcp`
	+ Limit by any subnet
		- `sudo ufw allow from 10.222.0.0/24 proto tcp to any port 22` 


# SCP - Secure copy

Example:

```bash
# scp <source> <destination>
scp debian.iso user@10.0.2.51:/home/user
```
- scp options:

```
-C      	Compression enable
-c cipher 	encript the data
-p port		use port number
-r			recursive
```

# rsync - better than scp

It can be used for local copying and remotely. for remote, you have to installed on both systems.
rsync reduces the amount of  data  sent  over the  network  by  sending only the differences between the source files and the existing files in the destination.

Example:

```bash
# copy <source> <destination> whit archive 
rsync -a debian.iso user@10.0.2.51:/home/user
```

in this example The files are transferred in archive mode, which ensures that symbolic links, evices, attributes, permissions, ownerships, etc are preserved in the transfer.

- rsync options:

```
-v      		verbos
-z		 		compress the data
-u          	skip files that are newer on the receiver
-P				progress
-e "ssh -p 22"	port change
```

# SFTP
the SSH File Transfer Protocol, also known as Secure File Transfer Protocol, is a network protocol that provides file access, file transfer, and file management on remote system.

- Connect to server: `sftp user@10.0.2.52`
- Select the public SSH file: `sftp -i <identify key file> <destination>`
- Retrieving a file: `get <filename> `
- Resume get: `reget <filename>`
- Pushing files to the remote system `put <filename>`
- list local system: `lls`

 > Note: Many commands can be run locally while in an SFTP session. Just place an 'l' before the command. For example, `cd` becomes `lcd`, `mkdir` becomes `lmkdir`, and `pwd` becomes `lpwd`. 

While in an SFTP session, press the ? key to access a basic help system and list of commands that you can run. 

When finished you can exit the session by typing `bye` or `quit` or by pressing Ctrl + d.

# curl

**curl**  is a tool to transfer data from or to a server, using one of the supported protocols (DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS, IMAP, IMAPS,  LDAP,  LDAPS,  MQTT, POP3, POP3S, RTMP, RTMPS, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET and TFTP). The command  is  designed to work without user interaction.

- for installing : `apt install curl`

```bash
# Save output to file
curl -o <output_file> <file_url>

# Multiple Download
curl -O <file_url1> -O <file_url2>

# Header request
curl -I liara.ir/robots.txt

# Access with redirect
curl -L <file_url>
```

# sshuttle - ssh tunnel
```bash
# Route all traffic 
sshuttle -r user@remote-server 0/0

# Route specific subnets. accessing remote to internal network
sshuttle -r user@remote-server 10.1.2.0/24

# use ssh key
sshuttle -r user@remote-server --ssh-cmd "ssh -i /path/to/keyfile" 0/0
```