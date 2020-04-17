# SSH Compatibility and Different Tricks.

SSH seems such a simple tool at times, SSH a username and a password and you have remote command line access to a machine, either through a nice certificate or a password. During my times in the PWK (OSCP) labs I came across several situations where I was trying to connect via SSH back to servers, where legacy crypto settings were enabled. typically I would end up with an error coming back such as;

```
kali@kali:~$ ssh uname@Host
Unable to negotiate with Host port 22: no matching key exchange method found. Their offer: diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1
```
After much head scratching and googling, I found that you can force SSH to use legacy protocols by a command line switch, for example;

```
kali@kali:~$ ssh uname@host -oKexAlgorithms=diffie-hellman-group-exchange-sha1
```

There are also further options that can be used  to allow your current SSH connection to use different options, i.e. legacy key exchange:

```
kali@kali:~$ ssh uname@host -oHostKeyAlgorithms=+ssh-dss
```

and not forgetting the ability to get SSH to tell you in detail, what is going on in the connection (Verbose Output)

```
kali@kali:~$ ssh uname@host -vvv
```

I also had an obscure error message in one case that was rather cryptic and didn't relate to the solution!, during connection to a remote server I kept getting the following error message during key exchange,

```
debug2: we sent a publickey packet, wait for reply
debug3: receive packet: type 51
debug1: Authentications that can continue: publickey,password
debug1: Trying private key: /home/kali/.ssh/id_dsa
debug3: no such identity: /home/kali/.ssh/id_dsa: No such file or directory
debug1: Trying private key: /home/kali/.ssh/id_ecdsa
debug3: no such identity: /home/kali/.ssh/id_ecdsa: No such file or directory
debug1: Trying private key: /home/kali/.ssh/id_ecdsa_sk
debug3: no such identity: /home/kali/.ssh/id_ecdsa_sk: No such file or directory
debug1: Trying private key: /home/kali/.ssh/id_ed25519
debug3: no such identity: /home/kali/.ssh/id_ed25519: No such file or directory
debug1: Trying private key: /home/kali/.ssh/id_ed25519_sk
debug3: no such identity: /home/kali/.ssh/id_ed25519_sk: No such file or directory
debug1: Trying private key: /home/kali/.ssh/id_xmss
debug3: no such identity: /home/kali/.ssh/id_xmss: No such file or directory
debug2: we did not send a packet, disable method
debug3: authmethod_lookup password
debug3: remaining preferred: ,password
```

namely this error line meant I spent quite a long time deep in Google trying to figure out what was going wrong,

```
debug2: we did not send a packet, disable method
```
Google (or rather a multitude of blog posts) were indicating this was a host configuration error with the private key files, however to cut a long story short, I had a wrong username for the authentication. Changing the Username to the valid one for the key and suddenly the connection sprang to life! Sounds like an obvious thing, but don't always believe a username written next to a key in a file you find on an Operating System!
