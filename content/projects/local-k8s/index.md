---
title: Local Kubernetes
---

There are several vendor implementations of a single-node Kubernetes cluster, such as Docker Desktop, OrbStack, and Rancher Desktop.
This project is my attempt to write my own (reinvent the wheel?) implementation, tailored to run in macOS M1.

I built this project while learning how to administer my own cluster. The first incarnation of this project is
[k8s-homenet](https://github.com/ginolatorilla/k8s-homenet). I found it tedious to maintain a general solution that can
deploy either a single or a multinode cluster, hence this project's reduced scope.

See the project's [{{< icon "github" >}} repo](https://github.com/ginolatorilla/local-platform) to know more.
