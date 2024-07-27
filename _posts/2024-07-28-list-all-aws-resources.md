---
layout: post
title: List All AWS Resources
tags: aws
---

AWS has a free service called **Resource Explorer** that allows you to view the resources in your
account. However, you need to set this up. If you need another quicker and more readily available
way, use **AWS Resource Groups & Tag Editor**.

![AWS Resource Groups and Tag Editor](/assets/img/aws-resource-and-tag-editor.png)

## Procedure

1. Go to the **Tag Editor** page in the AWS console.
2. Choose **All Regions** and **All supported resource types**.
3. Press the **Search resources** button.
4. (Optional) Press the **Export N resources to CSV** button to save it to a file.

This service lists all the resources in your account, including those that AWS created. For example,
you'll see the VPC-related resources, like route tables and DHCP options, which are pre-made for
all accounts.

![AWS Resource Groups and Tag Editor results](/assets/img/aws-resource-and-tag-editor-results.png)
