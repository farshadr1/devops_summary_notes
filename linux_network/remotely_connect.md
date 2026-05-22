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

