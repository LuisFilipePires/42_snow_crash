## Level01

---

```
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
           _____                      _____               _     
          / ____|                    / ____|             | |    
         | (___  _ __   _____      _| |     _ __ __ _ ___| |__  
          \___ \| '_ \ / _ \ \ /\ / / |    | '__/ _` / __| '_ \ 
          ____) | | | | (_) \ V  V /| |____| | | (_| \__ \ | | |
         |_____/|_| |_|\___/ \_/\_/  \_____|_|  \__,_|___/_| |_|
                                                        
  Good luck & Have fun

          192.168.1.50 
level01@192.168.1.50's 
password: x24ti5gi3x0ol2eh4esiuxias

level01@SnowCrash:~$  

```

---

Since I already investigated `/etc/passwd` during the previous level, I suspected that the password field could contain useful information.

The entry for `flag01` was:

flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash

The value:

```42hDRfypTqqnw```

looked suspicious, so I checked what type of hash it could be:

```hashid 42hDRfypTqqnw```

The result was:

- Analyzing '42hDRfypTqqnw'
- [+] DES(Unix)
- [+] Traditional DES
- [+] DEScrypt

This indicated that the value was probably a traditional Unix DES crypt hash.

DES crypt hashes use the following structure:

salt + hash

In this case:

```
42hDRfypTqqnw
^^
||
salt

```

The first two characters (42) are the salt, and the remaining characters are the hashed password.

I saved the hash into a file:


```
echo "42hDRfypTqqnw" > hash.txt

```

Then I used John the Ripper to crack the hash:

john hash.txt

John found the password:

abcdefg

To confirm the result:

```
john --show hash.txt

?:abcdefg

1 password hash cracked, 0 left

```

```
level01@SnowCrash:~$ su flag01
Password: 
Don't forget to launch getflag !
flag01@SnowCrash:~$ getflag
Check flag.Here is your token : f2av5il02puano7naaf6adaaf
flag01@SnowCrash:~$ 

```

---

### Next Password for ssh level02@192.168.1.50 -p 4242
password: f2av5il02puano7naaf6adaaf

---
