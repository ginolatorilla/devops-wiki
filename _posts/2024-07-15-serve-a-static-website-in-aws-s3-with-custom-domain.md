---
layout: post
title: Serve a Static Website in AWS S3 with a Custom Domain
tags: aws aws-s3 dns cloudflare
---

You can use AWS S3 to serve a static website, which is a feature according to the
{% include external-link.html text="official AWS S3 documentation" url="https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html" %}.
The recommendations here might not be compatible with your business.

## Problems with AWS recommendations

S3 websites can only serve HTTP, so they recommend you use and pay for **CloudFront** if you need HTTPS. Your business
may already have purchased a similar CDN service, so you incur another unnecessary expense.

They also suggest you name your bucket using its public-facing hostname (e.g. `bucket.example.com`).  According to the
{% include external-link.html text="bucket naming rules" url="https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucketnamingrules.html#general-purpose-bucket-names" %},
names are effectively unique in AWS, so there's potential for a name collision with other accounts.

Finally, they recommend using and paying **Route53** for the CNAME records. This added cost might not be an issue
unless your business already has a DNS provider.

## Solution

My solution has two parts: the **bucket**, which you'll provision and configure in AWS, and the **DNS**, which you'll configure
in your DNS provider.

### Provision and configure the bucket

Follow the {% include external-link.html text="official instructions to enable web hosting" url="https://docs.aws.amazon.com/AmazonS3/latest/userguide/EnableWebsiteHosting.html" %}
and with these settings

| Parameter                           | Value or Setting                        |
| ----------------------------------- | --------------------------------------- |
| Bucket Name                         | `<something-descriptive>-<random-hash>` |
| Properties > Static website hosting | Enable ✅                                |
| Permissions > Block Public Access   | Disable ❌                               |
| Permissions > Bucket Policy         | See the next paragraphs                 |

Choose a name for the bucket with a suffix to decrease the chances of name collisions.

Your DNS provider should provide you with a list of public IPs, like what's in
{% include external-link.html text="this page from CloudFlare" url="https://www.cloudflare.com/en-gb/ips/" %}.
When crafting the bucket policy, use those public IDs with the sample policy described in the
{% include external-link.html text="official instructions to restrict access to buckets" url="https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html#example-bucket-policies-IP" %}.
Here's a template policy you can use:

```json
{
    "Version": "2012-10-17",
    "Id": "S3PolicyId1",
    "Statement": [
        {
            "Sid": "IPAllow",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": [
                "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
            ],
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": [
                    "192.0.2.0/24 // ✍️ Replace this with the CIDRs of your DNS provider's public IPs"
                    ]
                }
            }
        }
    ]
}
```

Configure the index/error documents and the redirects as needed. These tasks are optional.

### Configure your DNS to point to your bucket's website endpoint

Let's assume your bucket has the following attributes.

| Parameter | Value                                                     |
| --------- | --------------------------------------------------------- |
| Name      | `my-website-5678`                                         |
| Region    | `us-west-2`                                               |
| Endpoint  | http://my-website-5678.s3-website.us-west-2.amazonaws.com |

Because of the bucket policy, you'll always get an `HTTP403` if browsing the website endpoint.

We'll also assume that your DNS provider is **CloudFlare** and your zone is `example.com`. We aim to have this
endpoint working and pointing to our bucket: **https://blog.example.com**

Configure the following resources for your zone:

- A **CNAME** record pointing to the host part of the S3 website endpoint, which must be **orange-proxied**, for example:
  
  ```plaintext
  CNAME blog my-website-5678.s3-website.us-west-2.amazonaws.com
  ```

- An **origin rule** that matches the hostname to `blog.example.com` and it should **override the host header** to `my-website-5678.s3-website.us-west-2.amazonaws.com`

- A **configuration rule** that matches the hostname to `blog.example.com` and should apply **Flexible SSL**.

After this, you should be able to view the S3-hosted website securely and with an appropriately named domain.

### Further explanation

The trick lies with how {% include external-link.html text="AWS parses the host header" url="https://docs.aws.amazon.com/AmazonS3/latest/userguide/VirtualHosting.html#VirtualHostingSpecifyBucket" %}
for to get the bucket name and the key (the URI path, which is also the path of an object in the bucket). The host header will take precedence over the hostname in the request, so this rule allows us to use different bucket names from the DNS hostnames. The CloudFlare origin rule provides us with a way to override host headers.

The CloudFlare configuration rule provides TLS coverage from the clients to AWS S3; the communication between
CloudFlare and AWS is still in HTTP. To improve security, we use a bucket policy that limits access from the public
to the AWS S3 bucket website HTTP endpoint.  Making the bucket name anonymous prevents name collision with other
AWS accounts.

## References

- {% include external-link.html text="Hosting a static website using Amazon S3" url="https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html" %}
- {% include external-link.html text="Host header override with CloudFlare origin rules" url="https://developers.cloudflare.com/rules/origin-rules/features/#host-header" %}
- {% include external-link.html text="Configuring an AWS static site to use CloudFlare" url="https://developers.cloudflare.com/support/third-party-software/others/configuring-an-amazon-web-services-static-site-to-use-cloudflare/" %}
- {% include external-link.html text="Adrian Drummond's blog post about AWS S3 bucket host header" url="https://adriandrummond.com/2018/11/18/aws-s3-bucket-host-header/" %}