# Level09

### Vulnerability: Reverse engineering a character transformation

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
level09@192.168.1.50's
password: 25749xKZ8L7DkSCwJkT9dyv6f
level09@SnowCrash:~$ 
```

First, I checked the files and permissions:

```bash
ls -al
```

The binary has the SUID bit enabled:

```text
-rwsr-sr-x 1 flag09 level09 7640 Mar 5 2016 level09
```

This means the program runs with the privileges of the `flag09` user.

As in previous levels, I started by analysing the binary:

```bash
strings level09
```

Some interesting strings appeared:

```
You need to provied only one arg.
You should not reverse this
ptrace
LD_PRELOAD
/proc/self/maps
level09.c
```

The presence of `level09.c` in the binary strings suggested that debugging information was included, but the source code itself was not available.

The program requires exactly one argument:

```bash
./level09 [argument]
```

I tested the behaviour:

```bash
./level09 "getflag"
```

Output:

```
gfvipfm
```

By comparing the input and output characters:

```
g -> g  (+0)
e -> f  (+1)
t -> v  (+2)
f -> i  (+3)
l -> p  (+4)
a -> f  (+5)
g -> m  (+6)
```

I identified that the program applies this transformation:

```c
output[i] = input[i] + i;
```

Therefore, to recover the original password from the token file, the reverse operation is required:

```c
input[i] = output[i] - i;
```

---

### Analysing the token file

Reading the token directly:

```bash
cat token
```

did not show a readable string because the file contains non-printable bytes.

To inspect the real content, I used:

```bash
xxd token
```

Output:

```
66 34 6b 6d 6d 36 70 7c 3d 82 7f 70 82 6e 83 82
44 42 83 44 75 7b 7f 8c 89 0a
```

The bytes above `0x7f` explain why `cat` displayed replacement characters (`�`).

Since the transformation used by the binary was known, I needed to apply the inverse operation to every byte.

---

### Creating a decrypt program

The current directory was not writable, so I used `/tmp`, which allows temporary files.

I wrote a small C program using low-level system calls (`open`, `read`, `write`) to reverse the transformation:

```c
#include <unistd.h>
#include <fcntl.h>

int main(void)
{
    int fd;
    char c;
    int i = 0;

    fd = open("/home/user/level09/token", O_RDONLY);
    if (fd == -1)
        return (1);

    while (read(fd, &c, 1) == 1)
    {
        if (c == '\n')
            break;

        c = c - i;
        write(1, &c, 1);
        i++;
    }

    write(1, "\n", 1);
    close(fd);

    return (0);
}
```

cd /tmp/

gcc decrypt.c -o decrypt

./decrypt

**output: f3iji1ju5yuevaus41q1afiuq**

After compiling and executing the program, it returned the password needed to access `flag09`.

Then:

```bash
su flag09
```

and:

```bash
getflag
```

returned the final token for this level.

```
level09@SnowCrash:~$ su flag09
Password: f3iji1ju5yuevaus41q1afiuq
Don't forget to launch getflag !
flag09@SnowCrash:~$ getflag
Check flag.Here is your token : s5cAJpM8ev6XHw998pRWG728z

```

---

### Key takeaway

The vulnerability is not a direct privilege escalation but a weak reversible transformation.

The program modifies each character by adding its position index. By analysing its behaviour and reversing the algorithm, it is possible to recover the original password from the token file.

---
### Next Password for ssh level10@192.168.1.50 -p 4242

### s5cAJpM8ev6XHw998pRWG728z
---
