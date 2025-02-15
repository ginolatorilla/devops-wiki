---
title: Dotfiles
summary: All my workstation's configuration is _GitOps_.
---
Run a quick search of the word "dotfiles" in GitHub, and you'll see many personal projects that use the same name.
I wrote my first version inspired from the ["dotfiles of \"Cowboy\" Ben Alman"](https://github.com/cowboy/dotfiles).
As I started improving it, the installer script got bulkier, and it's doing the work of a configuration manager.
Because of that reason, I decided to rework my dotfiles with **Ansible**, which now allows me to sync my app configs
and install the apps themselves. In DevOps terms, my workspace adopts the **GitOps** principle.

See the project's [{{< icon "github" >}} repo](https://github.com/ginolatorilla/dotfiles) to know more.
