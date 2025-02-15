---
title: Project Templates
---

I spent plenty of time setting up the file structure whenever I started projects. Because of that, I realised that not
repeating that initial task would save me time. Here are my project templates based on my work experience.

## C++

This project template is the first of many. I designed it to build C++ binaries with CMake. It also includes helpful
development tools like **Google Test**, **Address Sanitizers**, and **Clang Tidy**.

See the project's [{{< icon "github" >}} repo](https://github.com/ginolatorilla/cpp-cmake-template/) to know more.

## Python

This project template has a script that sets up a Python project that readily builds into a package compatible with `pip`.
It also comes with a virtual environment to supplement your development workflows.

See the project's [{{< icon "github" >}} repo](https://github.com/ginolatorilla/python3-template/) to know more.

## Terraform

The main feature of this project template is it can support multiple environments. For example, you can have
a production and staging environment in the same repo but backed by different Terraform states
that work well with **GitLab** and **Terraform Enterprise**. For anything shared between environments, there's a local modules directory.

See the project's [{{< icon "github" >}} repo](https://github.com/ginolatorilla/terraform-template/) to know more.

## Go

This template is for building command line apps with Go. The project structure follows recommendations from the
[Unofficial Standard Go Project Layout](https://github.com/golang-standards/project-layout). The following are the
frameworks and libraries used in this project:

- **Cobra** for the command line parser
- **Viper** for configuration
- **Zap** for structured logging

See the project's [{{< icon "github" >}} repo](https://github.com/ginolatorilla/go-template/) to know more.

## React/Gin web app

This template is a specialised version of the _Go project template_ for a single-page web application.

- **ReactJS** with **TypeScript** and **TailwindCSS** for the frontend
- **Gin** for the backend

See the project's [{{< icon "github" >}} repo](https://github.com/ginolatorilla/react-go-template/) to know more.
