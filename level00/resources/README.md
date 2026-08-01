# level00

```
1. Enumerate filesystem with ls.
2. Discover `/rofs` contains another filesystem tree.
3. Inspect `/etc/passwd` to identify users and home directories.
4. Verify permissions prevent accessing other levels.
5. Search for files/binaries that could allow privilege transition.
```

I started by using the command ```ls -al``` to inspect the root directory, 

At first, when I tried to list some directories, I received "Permission denied" errors. 
This happened because listing a directory requires read permission (`r`) on that directory.

The root directory `/` has the following permissions:

drwxr-xr-x

This means that all users can read and list its contents.

However, some directories have restricted permissions. For example:

d--x--x--x  root root  home

The directory `/home` only gives execute permission (`x`) to users. This allows users to access a directory if they already know the path, but they cannot list its contents with `ls`.

- With the `cd` command, I used the `TAB` key for auto-completion. This allowed me to see the available files and directories that I could access or traverse.

```
level00@SnowCrash:/$ ls -al
total 2
drwxr-xr-x  1 root root  240 Aug  1 12:25 .
drwxr-xr-x  1 root root  240 Aug  1 12:25 ..
drwxr-xr-x  2 root root 2814 Mar  9  2016 bin
drwxr-xr-x  1 root root   60 Aug 30  2015 boot
dr-xr-xr-x  7 root root 2048 Mar 12  2016 cdrom
drwxr-xr-x 14 root root 3960 Aug  1 12:26 dev
drwxr-xr-x  1 root root  260 Aug  1 12:25 etc
d--x--x--x  1 root root   80 Aug 30  2015 home
lrwxrwxrwx  1 root root   37 Aug 29  2015 initrd.img -> /boot/initrd.img-3.2.0-89-generic-pae
drwxr-xr-x 23 root root 1436 Aug 30  2015 lib
drwxr-xr-x  1 root root   60 Mar 12  2016 media
drwxr-xr-x  2 root root    3 Mar 12  2016 mnt
drwxr-xr-x  1 root root   60 Aug  1 12:26 opt
dr-xr-xr-x 88 root root    0 Aug  1 12:25 proc
drwxrwxrwx 21 root root  364 Mar 12  2016 rofs
drwx------  5 root root  165 Mar 12  2016 root
drwxr-xr-x 14 root root  540 Aug  1 20:23 run
drwxr-xr-x  2 root root 3469 Aug 30  2015 sbin
drwxr-xr-x  2 root root    3 Mar  5  2012 selinux
drwxr-xr-x  2 root root    3 Aug 29  2015 srv
drwxr-xr-x 13 root root    0 Aug  1 12:25 sys
d-wx-wx-wx  4 root root   80 Aug  1 22:10 tmp
drwxr-xr-x  1 root root   80 Aug 29  2015 usr
drwxr-xr-x  1 root root  160 Mar 12  2016 var
lrwxrwxrwx  1 root root   33 Aug 29  2015 vmlinuz -> boot/vmlinuz-3.2.0-89-generic-pae
level00@SnowCrash:/$ 

```

While inspecting the filesystem, I noticed the `/rofs` directory:

``` drwxrwxrwx root root rofs ```

Since this directory was readable and writable, I explored it to look for interesting files.

rofs is a snapshot of the /, so in here maybe i can find something

- level00@SnowCrash:/rofs/etc$ cat shadow
- cat: shadow: Permission denied

- I tried the command `cat passwd` inside `/rofs/etc` and found some valuable information about the system users.

```
level00@SnowCrash:/rofs/etc$ cat passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/bin/sh
man:x:6:12:man:/var/cache/man:/bin/sh
lp:x:7:7:lp:/var/spool/lpd:/bin/sh
mail:x:8:8:mail:/var/mail:/bin/sh
news:x:9:9:news:/var/spool/news:/bin/sh
uucp:x:10:10:uucp:/var/spool/uucp:/bin/sh
proxy:x:13:13:proxy:/bin:/bin/sh
www-data:x:33:33:www-data:/var/www:/bin/sh
backup:x:34:34:backup:/var/backups:/bin/sh
list:x:38:38:Mailing List Manager:/var/list:/bin/sh
irc:x:39:39:ircd:/var/run/ircd:/bin/sh
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/bin/sh
nobody:x:65534:65534:nobody:/nonexistent:/bin/sh
libuuid:x:100:101::/var/lib/libuuid:/bin/sh
syslog:x:101:103::/home/syslog:/bin/false
messagebus:x:102:106::/var/run/dbus:/bin/false
whoopsie:x:103:107::/nonexistent:/bin/false
landscape:x:104:110::/var/lib/landscape:/bin/false
sshd:x:105:65534::/var/run/sshd:/usr/sbin/nologin
level00:x:2000:2000::/home/user/level00:/bin/bash
level01:x:2001:2001::/home/user/level01:/bin/bash
level02:x:2002:2002::/home/user/level02:/bin/bash
level03:x:2003:2003::/home/user/level03:/bin/bash
level04:x:2004:2004::/home/user/level04:/bin/bash
level05:x:2005:2005::/home/user/level05:/bin/bash
level06:x:2006:2006::/home/user/level06:/bin/bash
level07:x:2007:2007::/home/user/level07:/bin/bash
level08:x:2008:2008::/home/user/level08:/bin/bash
level09:x:2009:2009::/home/user/level09:/bin/bash
level10:x:2010:2010::/home/user/level10:/bin/bash
level11:x:2011:2011::/home/user/level11:/bin/bash
level12:x:2012:2012::/home/user/level12:/bin/bash
level13:x:2013:2013::/home/user/level13:/bin/bash
level14:x:2014:2014::/home/user/level14:/bin/bash
flag00:x:3000:3000::/home/flag/flag00:/bin/bash
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
flag02:x:3002:3002::/home/flag/flag02:/bin/bash
flag03:x:3003:3003::/home/flag/flag03:/bin/bash
flag04:x:3004:3004::/home/flag/flag04:/bin/bash
flag05:x:3005:3005::/home/flag/flag05:/bin/bash
flag06:x:3006:3006::/home/flag/flag06:/bin/bash
flag07:x:3007:3007::/home/flag/flag07:/bin/bash
flag08:x:3008:3008::/home/flag/flag08:/bin/bash
flag09:x:3009:3009::/home/flag/flag09:/bin/bash
flag10:x:3010:3010::/home/flag/flag10:/bin/bash
flag11:x:3011:3011::/home/flag/flag11:/bin/bash
flag12:x:3012:3012::/home/flag/flag12:/bin/bash
flag13:x:3013:3013::/home/flag/flag13:/bin/bash
flag14:x:3014:3014::/home/flag/flag14:/bin/bash
level00@SnowCrash:/rofs/etc$ 
```

- The `/etc/passwd` file contains information about all users registered on the system.
  Each line follows this format:

username:password:UID:GID:comment:home_directory:shell

- While most users have `x` in the password field, which means the password hash is stored somewhere else (`/etc/shadow`), I noticed that the user `flag01` had a different value:

flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash

- This indicates that a password-related value was exposed directly in `/etc/passwd`, which is a security issue.
- The file also revealed the home directories of the different levels, for example:

level01:x:2001:2001::/home/user/level01:/bin/bash

- This information helped me understand the structure of the challenge and where the users' files were located.

--- 

- After finding the user home directory from `/etc/passwd`, I checked the corresponding directory:

cd /rofs/home/user/level00

ls -al

```
- The directory only contained standard shell configuration files:

  - `.bashrc`
  - `.profile`
  - `.bash_logout`
```

- No obvious password or flag file was found in the user's home directory.


# finding the flag

- i try ``` find / -user flag00 2>/dev/null ``` but received to mutch noise

- I searched for files belonging to the `flag00` group: ``` find / -group flag00 2>/dev/null ``` 


Result
```
/usr/sbin/john
/rofs/usr/sbin/john
```

```
level00@SnowCrash:/rofs/home/user/level00$ find / -group flag00 2>/dev/null
/usr/sbin/john
/rofs/usr/sbin/john
level00@SnowCrash:/rofs/home/user/level00$ cat /usr/sbin/john
cdiiddwpgswtgt
level00@SnowCrash:/rofs/home/user/level00$ cat /rofs/usr/sbin/john
cdiiddwpgswtgt

level00@SnowCrash:/rofs/home/user/level00$ getflag
Check flag.Here is your token : 
Nope there is no token here for you sorry. Try again :)
level00@SnowCrash:/rofs/home/user/level00$ getflag cdiiddwpgswtgt
Check flag.Here is your token : 
Nope there is no token here for you sorry. Try again :)


- so 'cdiiddwpgswtgt' is not the password, is not a md5, SHA, DES, its like caesar cipher

in site https://cryptii.com/pipes/caesar-cipher/ , decode with shif 11 a ->

- chipertext - "nottoohardhere" , but its kinding me

level00@SnowCrash:/rofs/home/user/level00$ su flag00
Password: nottoohardhere
Don't forget to launch getflag !
flag00@SnowCrash:~$ getflag
Check flag.Here is your token : x24ti5gi3x0ol2eh4esiuxias
flag00@SnowCrash:~$ 

```

Next Password for ```ssh level01@192.168.1.50 -p 4242```

password: x24ti5gi3x0ol2eh4esiuxias


