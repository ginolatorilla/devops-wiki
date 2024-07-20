---
layout: page
title: My Projects
permalink: /projects/
---

Here are the things I made that I like to share with the rest of the world.

## Project templates

I spent plenty of time setting up the file structure whenever I started projects. Because of that, I realised that not
repeating that initial task would save me time. Here are my project templates based on my work experience.

### Python

This project template has a script that sets up a Python project that readily builds into a package compatible with `pip`.
It also comes with a virtual environment to supplement your development workflows.

See the project's {% include external-link.html text="repo" url="https://github.com/ginolatorilla/python3-template/" %} to know more.

### Terraform

The main feature of this project template is it can support multiple environments. For example, you can have
a production and staging environment in the same repo but backed by different Terraform states
that work well with **GitLab** and **Terraform Enterprise**. For anything shared between environments, there's a local modules directory.

See the project's {% include external-link.html text="repo" url="https://github.com/ginolatorilla/terraform-template/" %} to know more.

### C++

This project template is the first of many. I designed it to build C++ binaries with CMake. It also includes helpful
development tools like **Google Test**, **Address Sanitizers**, and **Clang Tidy**.

See the project's {% include external-link.html text="repo" url="https://github.com/ginolatorilla/cpp-cmake-template/" %} to know more.

### React/Gin web app

I did a small hobby project in 2023 using this tech stack:

- **ReactJS** with **TypeScript** and **TailwindCSS** for the frontend
- **Gin** with **Golang** for the backend
- **MongoDB** for storage

Some goodies you'll find in this project template:

- Optimized for **VSCode**: There are background tasks that will watch changes to your files for both frontend and
  backend components and hot-reload the development server for you.
- **CORS** in dev mode: I added CORS as a configurable middleware because the frontend and backend servers will run on separate servers.
- **12-factor app**: This project template bootstraps an app following this software design methodology (at least, I think so).

See the project's {% include external-link.html text="repo" url="https://github.com/ginolatorilla/react-go-template/" %} to know more.

## GitLab CI Templates

Similar to project templates, I also found myself building **GitLab CI pipelines**. You can link to these templates from any
GitLab instance that can reach {% include external-link.html text="GitLab.com" url="https://gitlab.com/" %} or embedded in projects if otherwise.
You can also configure the pipelines with CI/CD variables.

See the project's {% include external-link.html text="repo" url="https://gitlab.com/ginolatorilla/gitlab-ci-templates" %} to know more.

### Python

This pipeline template works well with my Python project template. It will run unit tests and style checks, generate release tags,
and push to a PyPI instance. It will only run jobs in the `test` stage for quicker iteration in merge requests.

Demos:

- {% include external-link.html text="Merge request pipeline" url="https://gitlab.com/ginolatorilla/gitlab-ci-templates-python-example/-/pipelines/1077161927" %}
- {% include external-link.html text="Main pipeline with automated tagging" url="https://gitlab.com/ginolatorilla/gitlab-ci-templates-python-example/-/pipelines/1077162100" %}
- {% include external-link.html text="Main pipeline with manual tagging" url="https://gitlab.com/ginolatorilla/gitlab-ci-templates-python-example/-/pipelines/1136995865" %}, triggered by a
  {% include external-link.html text="release" url="https://gitlab.com/ginolatorilla/gitlab-ci-templates-python-example/-/releases/v0.0.3" %}

### Docker

This pipeline template implements what I [posted](/2024/06/16/run-docker-in-gitlab-docker-executor) about running Docker in GitLab.
The main feature here is that for merge requests, the pipeline builds Docker images, and after merging, it builds and
pushes to your container image registry.

Demos:

- {% include external-link.html text="Merge request pipeline" url="https://gitlab.com/ginolatorilla/gitlab-ci-templates-docker-example/-/pipelines/1077706151" %}
- {% include external-link.html text="Main pipeline" url="https://gitlab.com/ginolatorilla/gitlab-ci-templates-docker-example/-/pipelines/1077706151" %}

### Terraform

GitLab will obsolete their Terraform CI templates, so I kept them and optimised them for my needs. These templates have
multiple variants for simple Terraform projects to multiple stages or environments.

Demos:

- {% include external-link.html text="My CloudFlare config with a single Terraform state" url="https://gitlab.com/ginolatorilla/terraform-cloudflare-config" %}
- {% include external-link.html text="My AWS config with separate staging and production stages" url="https://gitlab.com/ginolatorilla/terraform-aws-free-tier" %}

## Dotfiles

Run a quick search of the word "dotfiles" in GitHub, and you'll see many personal projects that use the same name.
I wrote my first version inspired from the {% include external-link.html text="dotfiles of \"Cowboy\" Ben Alman" url="https://github.com/cowboy/dotfiles" %}.
As I started improving it, the installer script got bulkier, and it's doing the work of a configuration manager.
Because of that reason, I decided to rework my dotfiles with **Ansible**, which now allows me to sync my app configs
and install the apps themselves. In DevOps terms, my workspace adopts the **GitOps** principle.

See the project's {% include external-link.html text="repo" url="https://gitlab.com/ginolatorilla/dotfiles" %} to know more.

## Command-line programs

Some of them are useful for work, and some are for fun.

### Wordlesolver

During the COVID-19 pandemic, I got bored, so I wrote this program to ~~prevent damaging my winning streak~~ learn
about parallelism and performant Python coding by finding a solution to a **Wordle** game.

See the project's {% include external-link.html text="repo" url="https://gitlab.com/ginolatorilla/wordlesolver" %} to know more.

### Kubeclean

**kubeclean** removes config maps in a Kubernetes cluster if no pod references them. _It's still a work in progress_.

This program is also the first one I wrote in **Rust**, which I plan to continue using for my future projects.

See the project's {% include external-link.html text="repo" url="https://gitlab.com/ginolatorilla/kubeclean" %} to know more.
