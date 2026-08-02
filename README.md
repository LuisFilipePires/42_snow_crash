# Snow_crash

---

```
           _____                      _____               _     
          / ____|                    / ____|             | |    
         | (___  _ __   _____      _| |     _ __ __ _ ___| |__  
          \___ \| '_ \ / _ \ \ /\ / / |    | '__/ _` / __| '_ \ 
          ____) | | | | (_) \ V  V /| |____| | | (_| \__ \ | | |
         |_____/|_| |_|\___/ \_/\_/  \_____|_|  \__,_|___/_| |_|
                                                        
  Good luck & Have fun
  
```

---

### The fisrt level connect via ssh level00@ > IP < -p 4242

### password: level00

All others levels password is the flag captured from the last level

Enter Ip machine, this ip showed its for an example only: 192.168.1.50



- 0 - level00 -> ```ssh level00@192.168.1.50 -p 4242```
- password: ```level00```

- 1 - levell01 -> ```ssh level01@192.168.1.50 -p 4242```
- passowrd: ```x24ti5gi3x0ol2eh4esiuxias```

- 2- level02 -> ```ssh level02@192.168.1.50 -p 4242```
- password: ```f2av5il02puano7naaf6adaaf```

- 3- level03 ```ssh level03@192.168.1.50 -p 4242```
- password: ```kooda2puivaav1idi4f57q8iq```

- 4 - level04 -> ```ssh level04@192.168.1.50 -p 4242```
- password: ```qi0maab88jeaj46qoumi7maus```

- 5 - level05 -> ```ssh level05@192.168.1.50 -p 4242```
- password: ```ne2searoevaevoem4ov4ar8ap```

- 6 - level06 -> ```ssh level06@192.168.1.50 -p 4242```
- password: ```viuaaale9huek52boumoomioc```

- 7 - level07 -> ```ssh level07@192.168.1.50 -p 4242```
- password: ```wiok45aaoguiboiki2tuin6ub```

---

* **level00** → find a hidden file owned by the `flag00` group
* **level01** → crack a DES hash found in `/etc/passwd`
* **level02** → analyze network traffic from a `.pcap` file
* **level03** → exploit `setuid` permissions and perform PATH hijacking
* **level04** → exploit a CGI Perl command injection vulnerability
* **level05** → exploit a cron job running commands as another user
* **level06** → exploit a PHP code injection vulnerability caused by unsafe `preg_replace` usage


---

- 1 - Reconaissance
- 2 - Service Discovery
- 3 - Initial Access
- 4 - Exploitation
- 5 - Privilege escalation

---

It is highly recommended to use the SSH connection available on port 4242:

## SSH

Activate SSH on the victim machine, enabling permanently

``` sudo systemctl enable ssh ```

- Tells systemctl to start SSH service during every reboot
- Does not start the service immediatly
- This settings remain until you disable it ``` sudo systemctl disable ssh ```

Activate SSH service (current session only)

``` sudo systemctl start ssh ```

- Starts the SSH server (sshd) immediatly
- Allows others computers to connect to this machine via SSH
- Does not change the system configuration
- After reboot, the SSH service will be stopped again

Start now + Enable permanently

``` sudo systemctl enable --now ssh ```

This command is equivalent to running

``` 
sudo systemctl enable ssh
sudo systemctl start ssh
```

Disabling automatic startup

``` sudo systemctl disable ssh ```

Stop the running service immediatly

``` sudo systemctl disable --now ssh ```

---

It is highly recommended to use the SSH connection available on port 4242:
``` $> ssh level00@192.168.16.128 -p 4242 ```

## by default port 4242 is already configured and active in MV Snow_Crash

## port 4242

In the Snow Crash project, port 4242 is the port where SSH server inside the victim machine is listening

```
ssh level00@xxx.xxx.xxx.xxx -p 4242

- level00 -> the user account on the victim machine
- IP -> Ip address of Snow_Crash VM
- -p 4242 -> connect to SSH on port 4242 insted of the default port 22
```


<
## TCP port 22 by default

<details>

<summary> SSH TCP port 22 study </summary>

SSH TCP port normally uses port 22

To configure SSH server (sshd) to listem on port 4242

Edit the ssh configuratiion file:

``` sudo nano /etc/ssh/sshd_config ```

Find: ``` Port 22 ```

change it to : ``` Port 4242 ```

Save the file, then restart SSH

``` sudo systemctl restart ssh ```

### Verify that SSH is listening on port 4242

(on the victim machine)

``` sudo ss -tlnp | grep ssh ```

Should see something like

``` LISTEN 0 128 0.0.0.0:4242 ````

</details>

---

If the IP address is not visible, you can retrieve it using the
’ifconfig’ command once you are connected.

On the Snow_Crash MV

```ip a ``` -> exp: 192.168.1.50

Them from attacker machine:

``` ssh level00@192.168.1.50 -p 4242 ```

---

IMPORTANT: because we are using Bridge networking

The connection is direct:

Attacker PC (Kali)
     |
     | SSH 4242
     |
     V
Snow_Crash VM
     |
     ssh listning on port 4242
     
### To test from kali before connecting:

nmap -p 4242 192.168.1.50

Expected result:

``` 4242/tct open ssh ```

or ``` ping < victmim IP > ```

should see replies:

```
64 bytes from 192.168.1.50: icmp_seq=1 ttl=64 time=0.5 ms

64 bytes from 192.168.1.50: icmp_seq=2 ttl=64 time=0.4 ms

```

PING does not test SSH. it only tests ICMP connectivity

### To test SSH port 4242

```
nc -zv 192.168.1.50 4242

```
expected: connection to 192.168.1.50 4242 port [tcp/*] succeeded!

| Comand | Purpose |
|--------|---------|
| ping IP | check network connectivity |
| nmap / nc port 4242 | check SSH service |
| ssh -p 4242 | connect |


---

# 1 - Reconaissance

### Initial Reconnaissance

| Step | Command | Purpose |
|------|---------|---------|
| 1 | `ping 192.168.1.50' | Verify network connectivity |
| 2 | `nmap -p 4242 192.168.1.50` | Check SSH service port |
| 3 | `ssh -p 4242 level00@192.168.1.50` | Connect to the target |


ssh level00@192.168.1.50 -p 4242

The SSH server presented its ECDSA host key fingerprint:

ECDSA key fingerprint is: SHA256:xxxxxxxxxxxxxxxxx

The fingerprint was verified and accepted:

Are you sure you want to continue connecting (yes/no/[fingerprint])? yes

```
           _____                      _____               _     
          / ____|                    / ____|             | |    
         | (___  _ __   _____      _| |     _ __ __ _ ___| |__  
          \___ \| '_ \ / _ \ \ /\ / / |    | '__/ _` / __| '_ \ 
          ____) | | | | (_) \ V  V /| |____| | | (_| \__ \ | | |
         |_____/|_| |_|\___/ \_/\_/  \_____|_|  \__,_|___/_| |_|
                                                        
  Good luck & Have fun

          192.168.1.50
level00@192.168.1.50's password: 
Permission denied, please try again.
level00@192.168.1.50's password:  "  level00  " 

```

## Web Service Enumeration

``` curl level00@192.168.1.50 ```

returned: " It Works! "

| Service | Port | Status |
|---------|------|--------|
| HTTP | 80 | Open (default page) |
| SSH | 4242 | Open (Snow Crash access) |

password : level00

```level00@SnowCrash:~$ ls -al```

```
total 12
dr-xr-x---+ 1 level00 level00  100 Mar  5  2016 .
d--x--x--x  1 root    users    340 Aug 30  2015 ..
-r-xr-x---+ 1 level00 level00  220 Apr  3  2012 .bash_logout
-r-xr-x---+ 1 level00 level00 3518 Aug 30  2015 .bashrc
-r-xr-x---+ 1 level00 level00  675 Apr  3  2012 .profile
```


---

## To view each stage of the process, follow the steps in the README file located in each resources folder.


