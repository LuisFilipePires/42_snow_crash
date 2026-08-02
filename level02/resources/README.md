# level02

### Backspace/Delete (.) dot

---
```
           _____                      _____               _     
          / ____|                    / ____|             | |    
         | (___  _ __   _____      _| |     _ __ __ _ ___| |__  
          \___ \| '_ \ / _ \ \ /\ / / |    | '__/ _` / __| '_ \ 
          ____) | | | | (_) \ V  V /| |____| | | (_| \__ \ | | |
         |_____/|_| |_|\___/ \_/\_/  \_____|_|  \__,_|___/_| |_|
                                                        
  Good luck & Have fun

          192.168.1.50 
level02@192.168.1.50's
password: f2av5il02puano7naaf6adaaf 

level02@SnowCrash:~$  

```
---

level02@SnowCrash:~$ ls -al
total 24
dr-x------ 1 level02 level02  120 Mar  5  2016 .
d--x--x--x 1 root    users    340 Aug 30  2015 ..
-r-x------ 1 level02 level02  220 Apr  3  2012 .bash_logout
-r-x------ 1 level02 level02 3518 Aug 30  2015 .bashrc
----r--r-- 1 flag02  level02 8302 Aug 30  2015 level02.pcap
-r-x------ 1 level02 level02  675 Apr  3  2012 .profile


I opened the `level02.pcap` file with **Wireshark**.

The file contains a captured network session. To inspect the exchanged data, I selected one of the TCP packets and chose:

```
Follow → TCP Stream
```

This reconstructs the full conversation between the client and the server.

Among the terminal control characters and protocol data, the interesting part was:

```text
Linux 2.6.38-8-generic-pae (::ffff:10.1.1.2) (pts/10)

wwwbugs login:
levelX

Password:
ft_wandr...NDRel.L0L

Login incorrect
```

The login attempt reveals the username (`levelX`) and a partially visible password. The dots (`.`) are not literal characters; they represent terminal control characters (such as backspace/delete) that were transmitted during typing.

The goal of this level is to reconstruct the actual password from the captured TCP stream.

---

The dots (`.`) are not literal characters. They represent terminal control characters (such as backspace/delete) transmitted while the user was typing.

After reconstructing the input by applying the backspaces, the actual password is:

```text
ft_waNDReL0L
```

I then switched to the `flag02` user:

```bash
su flag02
```

After entering the recovered password, I executed:

```bash
getflag
```

to obtain the token for the next level.

---

### Next Password for ssh level03@192.168.1.50 -p 4242
password: kooda2puivaav1idi4f57q8iq

---

