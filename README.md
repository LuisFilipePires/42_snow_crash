# Snow_crash

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

## port 4242

In the Snow Crash project, port 4242 is the port where SSH server inside the victim machine is listening

```
ssh level00@xxx.xxx.xxx.xxx -p 4242

- level00 -> the user account on the victim machine
- IP -> Ip address of Snow_Crash VM
- -p 4242 -> connect to SSH on port 4242 insted of the default port 22
```

## TCP port 22

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
| nmap/nc port 4242 | check SSH service |
| ssh -p 4242 | connect |
















