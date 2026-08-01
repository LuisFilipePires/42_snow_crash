# level00

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

drwxrwxrwx root root rofs

Since this directory was readable and writable, I explored it to look for interesting files.
