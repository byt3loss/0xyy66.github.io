---
layout: cheatsheet
title: Cheatsheet
date: 2025-11-04
categories: []
tags: []
---


<article class="cheat-item" data-keywords="linux http lotl">

Craft an HTTP request with `/dev/tcp` when `curl` and `wget` are missing.

```sh
HOST=127.0.0.1; PORT=5000; WPATH=/index.html; exec 3<>/dev/tcp/$HOST/$PORT; printf "GET $WPATH HTTP/1.0\r\nHost: $HOST:$PORT\r\n\r\n" >&3; cat <&3
```

</article>