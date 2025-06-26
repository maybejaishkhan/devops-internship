# SSH - Secure Shell
A cryptographic network protocol that allows secure communication over an unsecure network. It has many uses: remote login, file transfer, remote execution, traffic tunneling/port forwarding and auth. Listens on port `22` by default.


- `ssh-agent`
- `ssh-add`
- `keychain`

Seahorse is the gnome GUI tool for SSH and GPG keys. 

# SCP - Secure Copy
`scp` is basically `cp` but over the network using SSH. It is used to securly copy file/directories between systems. Uses :22 by default.
- Add `-r <folder>` for copying folders.
- Add `-P <port>` for custom port (instead of 22).
- Add `-C` to enable compression.
- Add `-i <key>` to use SSH Key instead of password. 

```bash
scp <flags> source destination
```

1. Copy local -> remote: `scp file.txt user@remote:/home/user`
2. Copy remote -> local: `scp user@remote:/home/user file.txt`
3. Copy remote -> remote: `scp user@remote:/home/user user@remote:/home/user`

We can set host aliases in `~/.ssh/config` to simplify commands.
```
Host <servername>
    HostName <ipaddress>
    User <user>
    IdentityFile <key>
```

# Rsync - 

# SFTP - Secure File Transfer Protocol

# SSHFS - 