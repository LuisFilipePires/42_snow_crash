# Snow_crash

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

- 1 - Reconaissance
- 2 - Service Discovery
- 3 - Initial Access
- 4 - Exploitation
- 5 Privilege escalation

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

## 1 Reconaissance

### Initial Reconnaissance

| Step | Command | Purpose |
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
level00@192.168.1.50's password: 

```

---
















