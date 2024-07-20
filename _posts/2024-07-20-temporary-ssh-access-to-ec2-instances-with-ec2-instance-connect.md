---
layout: post
title: Temporary SSH access to EC2 instances with EC2 instance connect
tags: aws aws-ec2 bash
---

An SSH key is all you need to access a remote shell to any EC2 instance. When you launch instances, you attach an
**EC2 keypair** resource to it, which can already exist, or AWS will ask you to create one.

The security issue here is that if you don't rotate these keys, and if an unauthorised person gets one of those keys, they can
gain access to your servers. There's also an operational issue: you can permanently lose access to your machines if you
lose the private key (part of the EC2 keypair).

To solve these issues, AWS has a command that resembles how the `ssh-copy-id` command behaves: `aws ec2-instance-connect`.
After a minute, your public SSH key vanishes from the EC2 instance, which is secure.

There's a catch though: the EC2 instance must support EC2 instance connect. Instances with older AMIs won't work unless
you install EC2 instance connect. Generally, make sure that you meet all of these
{% include external-link.html text="requirements" url="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-connect-prerequisites.html#eic-prereqs-grant-permissions" %}.

## Inject your existing key

Run the following command to inject your public SSH key to an EC2 instance:

```shell
aws ec2-instance-connect send-ssh-public-key --instance-id <id> --instance-os-user ec2-user --ssh-public-key "$(cat ~/.ssh/<key>.pub)"
```

When that command succeeds, you should be able to SSH to the EC2 instance using one of its IP or DNS addresses.

## Inject a temporary key

Use this Bash script that securely opens an SSH session with a temporary key in any compatible EC2 instance. This script
is ideal for unattended scenarios, like cron jobs or CI pipelines.

```bash
set -e
set -o pipefail

instance=$1
PUB_IP=$(aws ec2 describe-instances --instance-ids $1 --query 'Reservations[*].Instances[*].PublicIpAddress' --output text)
TMPKEY=$(mktemp -d)

# Create the temporary SSH key without a passphrase.
ssh-keygen -f $TEMPKEY/id_rsa -N ''
aws ec2-instance-connect send-ssh-public-key --instance-id $instance --instance-os-user ec2-user --ssh-public-key "$(cat $TMPKEY/id_rsa.pub)" >/dev/null
ssh ec2-user@$PUB_IP -i $TMPKEY/id_rsa

# Delete the temporary key.
rm -rf $TMPKEY
```
