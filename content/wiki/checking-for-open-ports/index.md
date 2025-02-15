---
date: 2024-03-08
title: Checking for Open Ports
tags: [networking]
---

Here are different ways to check if a port is open. Not all of these commands may be available on every system.

## nc (netcat)

```sh
nc -z <host> <port>
nc -z <host> <port-range-lowerbound>-<port-range-upperbound>
```

Warning: `nc` will always show UDP ports as open.

## telnet

```sh
telnet <host> <port>
```

Telnet is not script-friendly.
It opens an interactive terminal that you must quit by pressing the escape characters and sending the `quit` command.

```sh
$ telnet github.com 80
Trying 20.205.243.166...
Connected to github.com.
Escape character is '^]'.
^]
telnet> quit
Connection closed.
```

## curl

```sh
curl <host>:<port>
```

It's also handy to pass `-v` so that you'll see a confirmation if the port is open when the command shows no output and
appears to hang up.

```sh
$ curl -v localhost:53
*   Trying [::1]:53...
* Connected to localhost (::1) port 53
> GET / HTTP/1.1
> Host: localhost:53
> User-Agent: curl/8.4.0
> Accept: */*
>
^C
```
