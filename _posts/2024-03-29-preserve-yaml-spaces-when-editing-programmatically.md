---
layout: post
title: Preserve Spaces When Editing a YAML File Programmatically
tags: yaml scripting
---

When you are editing YAML files as part of some automation (e.g., a Python script that parses and edits some keys),
there is a high chance that the whitespaces may be lost. These spaces do not have any functional impact as far as
machines are concerned, but they may have value for humans. According to its {% include external-link.html text="official specification" url="https://yaml.org" %},
YAML is a "human-friendly" language; spaces are an excellent tool for readability if used correctly.

## Problem

```yaml
apiVersion: v1
kind: Pod
                                        # 👈 Blank lines before the metadata
metadata:
  name: keepalived
  namespace: kube-system
                                        # 👈 Blank lines before the pod spec
spec:
  containers:
    - image: osixia/keepalived:1.3.5-1
      name: keepalived
      volumeMounts:
        - mountPath: /usr/local/etc/keepalived/keepalived.conf
          name: config
        - mountPath: /etc/keepalived/
          name: check
```

Consider the excerpt for a Kubernetes pod manifest above. The two blank lines separate the root-level blocks
(`metadata` and `spec`), making them easier to read. For example, you need to edit some keys as part of an automation.

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: keepalived
    namespace: kube-system
spec:
  containers:
  - image: osixia/keepalived:1.3.5-2
    name: keepalived
    volumeMounts:
    - mountPath: /usr/local/etc/keepalived/keepalived.conf
      name: config
    - mountPath: /etc/keepalived/
      name: check
```

Here are some issues after processing:

- All blank lines are gone.
- The indentation style has changed.

These items result from using programs that parse YAML and use their formatting when saving.
There is no known way of storing formatting in YAMLs. This might not be an issue, but if you push this
file to version control, the repository owners may find it unpleasant due to its inconsistency with their coding style.

## Solution

```sh
get_original_yaml >current.yaml
edit_the_yaml >modified.yaml <current.yaml

# Preserve original whitespaces
compact_the_yaml <current.yaml >current-compact.yaml
diff -U0 -w -b --ignore-blank-lines current-compact.yaml modified.yaml \
    | patch --ignore-whitespace current.yaml -o modified-preserve-ws.yaml
```

This script shows the algorithm for preserving spaces in YAML using the core programs `diff` and `patch`.
`edit_the_yaml` and `compact_the_yaml` are using the same parsing and formatting functions. For example,
you can substitute them with `yq -r '.key="value"'` and `yq`, respectively
({% include external-link.html text="yq" url="https://mikefarah.gitbook.io/yq" %} is a command-line tool for editing YAMLs, similar to `jq` and `sed`).
The `modified-preserve-ws.yaml` is what you need: modified with spaces maintained.
