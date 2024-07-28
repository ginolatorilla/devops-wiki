---
layout: post
title: Force Deleting Kubernetes Pods
tags: kubernetes
---

Deleting a Kubernetes resource should be as simple as running `kubectl delete`. There are situations
like a power outage that could corrupt the cluster, leaving you with stray pods you can't delete. These pods,
if left unattended, will prevent you from draining nodes.

## Force delete with kubectl

Most of the time, if you call `kubectl delete --force --grace-period=0`, it will delete the stuck pod.

## Remove finalizers

Finalizers will prevent you from deleting resources. For example, consider this pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2024-07-13T05:58:00Z"
  finalizers:
    - github.com/ginolatorilla  # 👈 Finalizer here will block deletes
  generateName: busybox-28680838-
  name: busybox-28680838-qrkwb
  namespace: default
  resourceVersion: "708268"
  uid: eb28b021-7a59-4ad5-8158-b74ffe84c6e3
spec:
  # ...
```

Deleting that pod won't have any effect, even if we force it.

```shell
$ kubectl delete -n default pod busybox-28680838-qrkwb --force --grace-period=0
Warning: Immediate deletion does not wait for confirmation that the running resource has been terminated. The resource may continue to run on the cluster indefinitely.
pod "busybox-28680838-qrkwb" force deleted
# Shell remains stuck here waiting
```

Kubernetes will wait for the finalizer to disappear, so you have to remove it manually:

```shell
$ kubectl patch -n default pod busybox-28680838-qrkwb -p '{"metadata": { "finalizers": null }}'
pod/busybox-28680838-qrkwb patched

$ kubectl get -n default pod
No resources found in default namespace.
```

## Disconnect controllers that inject additional containers

Controllers like {% include external-link.html text="Istio" url="https://istio.io/" %} and
{% include external-link.html text="HCV Agent Injector" url="https://developer.hashicorp.com/vault/docs/platform/k8s/injector" %}
can inject sidecars or init containers. It's possible that you can't remove pod finalizers because as you update the pod's
manifest, the controllers will try to add containers.

```shell
$ kubectl patch -n default pod busybox-28680838-qrkwb -p '{"metadata": { "finalizers": null }}'
The Pod "busybox-28680838-qrkwb" is invalid: spec.initContainers: Forbidden: pod updates may not add or remove containers
```

It would help to make the injecting controller _ignore_ the pod you want to delete. For example, let's consider
this manifest that uses HCV Agent Injector annotations. You must remove them and the finalizer, which you can
quickly do with `kubectl edit`.

```shell
$ export EDITOR=vim                                     # 👈 Change this to your preference.
$ kubectl edit -n default pod busybox-28680838-qrkwb
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2024-07-13T05:58:00Z"
  annotations:
    vault.hashicorp.com/agent-inject: "true"            # 👈 Delete, and all other related annotations
  finalizers:
    - github.com/ginolatorilla                          # 👈 Delete
  generateName: busybox-28680838-
  name: busybox-28680838-qrkwb
  namespace: default
  resourceVersion: "708268"
  uid: eb28b021-7a59-4ad5-8158-b74ffe84c6e3
spec:
  # ...
```

## References

- {% include external-link.html url="https://istio.io/latest/docs/setup/additional-setup/sidecar-injection/" %}
- {% include external-link.html url="https://developer.hashicorp.com/vault/docs/platform/k8s/injector/annotations" %}
