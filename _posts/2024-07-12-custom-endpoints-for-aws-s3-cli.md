---
layout: post
title: Custom Endpoints for AWS S3 CLI
tags: aws s3
---

You can use the AWS S3 CLI with compatible object storage services like {% include external-link.html text="NooBaa" url="https://www.noobaa.com" %},
{% include external-link.html text="Rook" url="https://rook.io" %}, and {% include external-link.html text="MinIO" url="https://min.io" %}
using the following template for the commands:

```shell
export AWS_ACCESS_KEY_ID=<endpoint-username>
export AWS_SECRET_ACCESS_KEY=<endpoint-password>
aws --endpoint-url <endpoint-url> s3 <command>
```

To demonstrate, we'll set up MinIO with a container:

```shell
docker run -p 9000:9000 -p 9001:9001 quay.io/minio/minio server /data --console-address ":9001"
```

Browse to `http://localhost:9001` and log in with `minioadmin:minioadmin`.

![Create a bucket](/assets/img/minio-create-bucket.png)

Create a bucket (we'll call ours _mybucket_).

![New bucket called mybucket](/assets/img/minio-new-bucket-mybucket.png)

We can view the bucket using these commands:

```shell
$ export AWS_ACCESS_KEY_ID=minioadmin
$ export AWS_SECRET_ACCESS_KEY=minioadmin
$ aws --endpoint-url http://localhost:9000 s3 ls
2024-07-12 00:02:03 mybucket
```

Uploading a file should work, and we can view it with the AWS CLI:

```shell
$ echo hello | aws --endpoint-url http://localhost:9000 s3 cp - s3://mybucket/hello.txt
$ aws --endpoint-url http://localhost:9000 s3 ls s3://mybucket
2024-07-12 00:30:20          6 hello.txt
```

Here is the same file viewed in the MinIO UI:

![New bucket called mybucket](/assets/img/minio-view-file-in-mybucket.png)
