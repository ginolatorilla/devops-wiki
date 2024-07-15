---
layout: post
title:  SSH Tunnels
tags: networking ssh
---

Tunneling with SSH can be the answer to these questions:

- Do you need to access websites in other networks, and you can SSH into one of its hosts?
- Do you need an alternative route to a server?

![SSH tunnel overview](/assets/img/ssh-tunnel-overview.png)

Let's have a simple example where you can access a host in a private network (e.g., corporate) using a VPN.
A server---that you don't have access to---hosts a website; any other host in the network can access it.

## Procedure

Run the following command in the terminal.

```sh
ssh -L 8080:10.0.1.127:80 jump.xyz.corp -f -N
```

The command will launch an `ssh` process and will bind to `localhost:8080`.
`ssh` will connect to the jump host and forward any traffic from `locahost:8080` to `10.0.1.127:80`, the web server you want to access.
`-f` will make `ssh` run in the background, and `-N` tells it not to run anything in the jump host.

This method also works with any TCP server.

## References

- {% include external-link.html url="https://linuxize.com/post/how-to-setup-ssh-tunneling/" %}
