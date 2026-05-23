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

in client system:
```bash
ssh-keygen
```

It's also highly recommended to select and confirm a complex passphrase to protect the keypair. The public and private key store in home/user/.ssh/ directory. You can see three files inside of .ssh: id_rsa (which is the private key), id_rsa.pub (which is the public key to be copied to remote hosts), and known_hosts, which contains the list of known hosts that you have connected to in the past. 

> Tip: You can make bigger keys if your organization requires it. Let's say you needed 4096-bit. You could type the following:

	`ssh-keygen -b 4096`

Remember to use different names/directories when creating subsequent keys. 

```bash
ssh-copy-id user@10.0.2.51
```

This way, we don't have to send a user's password over the network, and instead rely on the much more secure SSH key process.

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

