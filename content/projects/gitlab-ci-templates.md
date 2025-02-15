---
title: GitLab CI Templates
summary: These are reusable pipeline code for your GitLab CI needs.
---
Similar to project templates, I also found myself building **GitLab CI pipelines**. You can link to these templates from any
GitLab instance that can reach [GitLab.com](https://gitlab.com/) or embedded in projects if otherwise.
You can also configure the pipelines with CI/CD variables.

See the project's [{{< icon "gitlab" >}} repo](https://gitlab.com/ginolatorilla/gitlab-ci-templates) to know more.

## GitLab CI for Python

This pipeline template works well with my Python project template. It will run unit tests and style checks, generate release tags,
and push to a PyPI instance. It will only run jobs in the `test` stage for quicker iteration in merge requests.

Demos:

- [Merge request pipeline](https://gitlab.com/ginolatorilla/gitlab-ci-templates-python-example/-/pipelines/1077161927)
- [Main pipeline with automated tagging](https://gitlab.com/ginolatorilla/gitlab-ci-templates-python-example/-/pipelines/1077162100)
- [Main pipeline with manual tagging](https://gitlab.com/ginolatorilla/gitlab-ci-templates-python-example/-/pipelines/1136995865), triggered by a
  [release](https://gitlab.com/ginolatorilla/gitlab-ci-templates-python-example/-/releases/v0.0.3)

## GitLab CI for Docker

This pipeline template implements what I [posted](/2024/06/16/run-docker-in-gitlab-docker-executor) about running Docker in GitLab.
The main feature here is that for merge requests, the pipeline builds Docker images, and after merging, it builds and
pushes to your container image registry.

Demos:

- [Merge request pipeline](https://gitlab.com/ginolatorilla/gitlab-ci-templates-docker-example/-/pipelines/1077706151)
- [Main pipeline](https://gitlab.com/ginolatorilla/gitlab-ci-templates-docker-example/-/pipelines/1077706151)

## GitLab CI for Terraform

GitLab will obsolete their Terraform CI templates, so I kept them and optimised them for my needs. These templates have
multiple variants for simple Terraform projects to multiple stages or environments.

Demos:

- [My CloudFlare config with a single Terraform state](https://gitlab.com/ginolatorilla/terraform-cloudflare-config)
- [My AWS config with separate staging and production stages](https://gitlab.com/ginolatorilla/terraform-aws-free-tier)
