---
layout: post
title: Run Docker in macOS Using Lima
tags: docker macos lima
---

{% include external-link.html text="Docker Desktop" url="https://www.docker.com/products/docker-desktop/" %}
is arguably the most popular tool for installing Docker in macOS. If you cannot use it due to technical constraints,
other alternatives exist, like {% include external-link.html text="Rancher Desktop" url="https://docs.rancherdesktop.io" %}.
If you want an automation-friendly solution, you can use {% include external-link.html text="Lima" url="https://lima-vm.io" %}.

Lima is a tool that can run virtual machines and is the same (if not inspired) tech behind Rancher Desktop.
MacOS doesn't support native container runtimes, so it needs Docker to run inside Linux in virtual machines.
You can install it with `brew` using this command.

```sh
brew install lima
```

You also need to install the Docker CLI and a credential helper for the macOS keychain:

```sh
brew install docker docker-credential-helper
```

Their documentation provides an {% include external-link.html text="example" url="https://lima-vm.io/docs/examples/#running-containers" %}
of how to set up Docker.

Here is my setup, which has the following features:

- VM will use {% include external-link.html text="native virtualisation" url="https://developer.apple.com/documentation/virtualization" %},
  which runs with an ARM CPU architecture, but it can use Rosetta for AMD binaries with a minor performance penalty.
- VM will use `virtiofs` to improve the performance of host-mounted directories.
- Allows you to mount directories in your home directory that you can write to.

```sh
limactl start --name docker \
    --memory 4 \
    --cpus 2 \
    --rosetta \
    --vm-type vz \
    --disk 40 \
    --mount-type=virtiofs \
    --mount-writable \
    --tty=false \
    template://docker-rootful
```

This command will create and start a VM called "docker" with 4GiB of memory, 2 CPU cores, and 40GiB of disk space.
You can tune these resource settings to your liking. This command should take less than 2 minutes to provision.
As of writing, `limactl 0.20.0` might show this error message `[hostagent] r.CreateEndpoint() = connection was refused`,
but you can safely ignore them. Don't forget to run the final instructions.

```sh
docker context create lima-docker --docker "host=unix:///Users/<you>/.lima/docker/sock/docker.sock"
docker context use lima-docker
docker run hello-world
```

With this command, you can test that everything works if you can see the files in your current directory.

```sh
docker run -it --rm -v $(pwd):/data busybox ls /data
```
