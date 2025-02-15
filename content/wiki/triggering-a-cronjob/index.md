---
date: 2024-12-31
title: Triggering a CronJob
tags: [kubernetes]
---

Run this command to trigger a CronJob resource in Kubernetes:

```shell
kubectl create job --from=cronjob/CRONJOB JOB_NAME -n NAMESPACE
```

This command works because it will use the job template (`spec.jobTemplate`)
from an existing `CRONJOB`. You will need to supply `JOB_NAME`, which must be unique considering
all other Jobs in the same `NAMESPACE`.

You can use this to test if a CronJob works without changing its execution (i.e. cron) schedule.
