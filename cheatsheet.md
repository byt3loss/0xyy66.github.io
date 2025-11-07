---
layout: cheatsheet
title: Cheatsheet
date: 2025-11-04
categories: []
tags: []
---

<article class="cheat-item" data-keywords="linux http lotl docker-escape" markdown="1">

### HTTP via /dev/tcp - LOTL

Craft an HTTP request with `/dev/tcp` when `curl` and `wget` are missing. Very useful when stuck in a minimal Docker container.

```sh
HOST=127.0.0.1; PORT=5000; WPATH=/index.html; exec 3<>/dev/tcp/$HOST/$PORT; printf "GET $WPATH HTTP/1.0\r\nHost: $HOST:$PORT\r\n\r\n" >&3; cat <&3
```

</article>

<article class="cheat-item" data-keywords="linux reverse-shell" markdown="1">

### Stabilize a reverse shell

My favourite method - very minimal, no Python needed. Upgrades a simple shell to a fully interactive TTY.

```sh
SHELL=/bin/bash script -q /dev/null
# press CTRL+Z to put the current process in background and run stty on your host
stty raw -echo; fg
reset
xterm
```

</article>


<article class="cheat-item" data-keywords="ffuf http dir discovery recon bruteforce" markdown="1">

### FFUF

#### Clusterbomb & Pitchfork modes

Use FFUF in clusterbomb mode to brute-force a login form. Faster than Burp Intruder in the community version.

```sh
ffuf -request req.txt -request-proto https -mode clusterbomb -w users.txt:FUZZUSER -w passwords.txt:FUZZPWD -fr "Invalid username or password."
```

Use FFUF in pitchfork mode to brute-force a login form for user enumeration, rotating the X-Forwarded-For header so each request appears to come from a different IP to bypass rate limits. In the same attack, FFUF supplies a long password and filters out responses that take less than 1200 milliseconds to complete to exploit a response timing-based vulnerability (the webapp calculates the hash for the provided password only if the supplied user exists in the DB).

```sh
ffuf -request req_userenum.txt -mode pitchfork -request-proto https -w usr.txt:FUZZUSR -w ips.txt:FUZZIP -t 2 -ft "<1200"
```

#### FFUF through Burpsuite

Route FFUF requests through Burp Suite - useful for debugging them or manipulating them with a Burp extension.

```sh
ffuf -request req.txt -request-proto https -replay-proxy http://127.0.0.1:8080 -t 2
```

</article>
