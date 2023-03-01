# Deploying An SSH Key The Fast Way

There is a command called ssh-copy-id. It basically does all the deploy steps automatically. 
You still need to generate the SSH key using `ssh-keygen -t ed25519 -C "your@email.com"`

ssh-copy-id <user>@<ip address>

Be sure to replace <user> with the remote username and <ip address> with the IP address of the remote host.

Check it out in action below.

```
pi@local_pi:~ $ ssh-copy-id pi@192.168.1.157
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/pi/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
pi@192.168.1.157's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'pi@192.168.1.157'"
and check to make sure that only the key(s) you wanted were added.

pi@local_pi:~ $
```
