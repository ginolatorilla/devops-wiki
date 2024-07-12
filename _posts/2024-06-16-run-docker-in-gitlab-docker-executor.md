---
layout: post
title: Run Docker in GitLab Docker Executors
tags: gitlab docker
---

GitLab's CI uses [runners](https://docs.gitlab.com/runner/){:target="_blank"}{:rel="noopener noreferrer"} to "run" jobs.
A runner is a service GitLab already [provides](https://docs.gitlab.com/ee/ci/runners/index.html){:target="_blank"}{:rel="noopener noreferrer"}, or you can
host your own. Runners use [executors](https://docs.gitlab.com/runner/#executors){:target="_blank"}{:rel="noopener noreferrer"}, which determine the job's environment.
The most widely used is the [Docker executor](https://docs.gitlab.com/runner/executors/docker.html){:target="_blank"}{:rel="noopener noreferrer"}, where jobs run
inside a Docker container.

Running Docker commands inside Docker containers is not a simple task. There are many ways to do it; my solution
does not require changes to the GitLab runners.

```yaml
job_that_uses_docker:
  before_script:
    # This is the part where you prepare the credentials if you need to push or pull from a private image registry.
    - docker login -u $DOCKER_LOGIN_USERNAME -p $DOCKER_LOGIN_PASSWORD $DOCKER_REGISTRY
  services:
    - name: docker:dind
      alias: docker
  # You can replace the image with anything that has the Docker CLI installed.
  image: docker:dind
  variables:
    DOCKER_HOST: tcp://docker:2375
    DOCKER_TLS_CERTDIR: ""
  script:
    # You can now call any Docker command here.
    - docker build <dockerfile-dir>
    - docker push <repo/image:tag>
```

This solution works by running the Docker Engine as a background [`service`](https://docs.gitlab.com/ee/ci/yaml/#services){:target="_blank"}{:rel="noopener noreferrer"}
for the duration of the job. The variable `DOCKER_HOST` ensures that the job can connect to the service, which gets a
DNS record from the service's `alias` keyword. The variable `DOCKER_TLS_CERTDIR` is set to an empty string to bypass
TLS certificate verification. It shouldn't be an issue since the service is only used locally and is not listening to
external connections.

The only disadvantage this has is that you cannot preserve any Docker resources
(e.g., images, containers, volumes, networks) you'll make during the job since the service will have ephemeral storage.
