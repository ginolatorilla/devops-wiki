---
layout: post
title: Deleting a Namespace Gracefully
tags: kubernetes
---

When you delete a Kubernetes namespace, the cluster will start removing each resource within. If there are finalizers
in the resources or the namespace, the cluster will wait for them to be cleared. If the controller that handles these
finalizers stop working, then the `kubectl delete` command will be stuck.

For example, you installed an operator, but it aborted and now you are left with resources from an unfinished installation.

## Procedure

1. Run `kubectl describe ns NAMESPACE`. You should see that the status is `Terminating` and one of the conditions will
   look something like this:

   ```plaintext
   NamespaceContentRemaining                    True    Wed, 01 Jan 2025 14:05:39 +0800  SomeResourcesRemain   Some resources are remaining: configmaps. has 1 resource instances
   NamespaceFinalizersRemaining                 True    Wed, 01 Jan 2025 14:05:39 +0800  SomeFinalizersRemain  Some content in the namespace has finalizers remaining: example/test in 1 resource instances
   ```

2. List all resources in the namespace and look for finalizers. `kubectl get all` will not show everything, but you can
   try `kubectl get` with the resources reported by `kubectl api-resources`.

3. For each finalizer you find, try to identify its controller. It could be running in the same namespace or in
   another one—the best thing you can do is to search for its documentation in the internet to identify it.

4. Try to resolve the issue in the controller if you found it. If not, then delete the finalizer in each resource with
   `kubectl patch -n NAMESPACE KIND/RESOURCE -p '{"metadata": {"finalizers": null}}'`.

5. Step 4 might fail if resources prevent patches. Here are some of the reasons:

   - You do not have the right permissions. Ask an administrator to grant you the permissions to delete and patch resources.
   - A validating webhook configuration is preventing you from removing finalizers. Create a backup with
     `kubectl get validatingwebhookconfigurations CONFIG -oyaml > backup.yaml`, then delete it with
     `kubectl delete validatingwebhookconfigurations CONFIG`. Restore the webhook if necessary with `kubectl create -f backup.yaml`.
   - A controller is trying to patch the resource as soon as you remove the finalizers. Try deleting annotations in the
     resource so the controller will ignore it. You can also scale in those controllers to zero with `kubectl scale --replicas=0`.
   - A CD service, like ArgoCD, is reversing your patches. Consult with their documentation to stop their service from interrupting.
     For example, with ArgoCD you can delete the Application custom resources that _own_ the resource you're trying to patch.

6. As a last resort, remove the finalizer in the namespace if there are no more resources within.

It's best practice not to force delete a Kubernetes namespace if it still has resources inside.

## References

- [Overview of Namespaces, the Kubernetes Documentation](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
- <https://github.com/kubernetes/kubectl/issues/151#issuecomment-402003022>