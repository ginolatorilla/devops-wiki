---
date: 2025-02-28
title: Printing Tables in the Terminal
tags: [bash]
---

With the [`column`](https://man7.org/linux/man-pages/man1/column.1.html) utility, you can write
scripts that display outputs similar to `kubectl`. If you couple this with [`jq`](https://jqlang.org/)
or [`yq`](https://mikefarah.gitbook.io/yq), then you can translate ugly pages of JSON to pretty
and readable (and highly customisable) tables.

## Example: list pods with mounted PVCs

### Script

```shell
kubectl get pods -A -ojson |
jq -r '
    ["NAMESPACE", "NAME", "PVC"],
    (
        .items[] |
        select(.spec.volumes[].persistentVolumeClaim) |
        [
            .metadata.namespace,
            .metadata.name,
            (.spec.volumes[].persistentVolumeClaim.claimName | select(.))
        ]
    ) |
    @tsv' |
column -t
```

### Output

```tsv
NAMESPACE  NAME                       PVC
minio      minio-0                    minio-0
jenkins    jenkins-0                  jenkins
mytests    test-app-56bdb5cb59-t5f6m  test-app
vault      vault-0                    data-vault-0
```

### Breakdown

`kubectl get pods -A -ojson` gets all the cluster's pods and displays them in JSON format.

`jq -r` receives that JSON stream from `kubectl` and runs it against the filters (the multiline,
single-quoted string).

`["NAMESPACE", "NAME", "PVC"],` is where we define our table column headers. The trailing comma
means were creating a new output "object".

`.items[]` iterates over every pod listed by `kubectl`. When you list resources by type
(`kubectl get RESOURCE ...`), it will return a list object, and every list object has an `items` field.

`select(.spec.volumes[].persistentVolumeClaim)` filters pods that have a PVC listed in its volume list.
When JQ sees an empty `volumes` or a non-existent `.persistentVolumeClaim`, it generates a `null`
value, which is *falsy*.

`[.metadata.namespace, .metadata.name, ...]` is where we have the equivalent of a JS or Haskell `map`
function that transforms a pod object to an array—a row in our table.

`(.spec.volumes[].persistentVolumeClaim.claimName | select(.))`, similar to the earlier `select(...)`
filter, we get all the PVCs as defined by the pod. The trailing `| select(.)` removes null objects.
When combined with the array (the table row), this flattens out to multiple rows. So for example,
if the pod has N PVCs, then this filter will generate N rows.

`@tsv` formats the arrays to TSV format. All of the left-hand side inputs need to be arrays—our
column headers and every generated row entry.

`column -t` is the final stage of this pipeline. `jq` displays a TSV output, but is not aligned.
The column program takes all of those outputs and reformats them by padding the columns as needed,
producing the pretty table you saw earlier.

This is what the table looks like if we remove `column -t` in the pipeline:

```tsv
NAMESPACE	NAME	PVC
minio	minio-0	minio-0
jenkins	jenkins-0	jenkins
mytests	test-app-56bdb5cb59-t5f6m	test-app
vault	vault-0	data-vault-0
```
