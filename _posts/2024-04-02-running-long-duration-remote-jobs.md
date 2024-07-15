---
layout: post
title: Running Long-Duration Remote Jobs
tags: ssh
---

Here are ways to run long-duration jobs in an SSH session.

## ️⚠️ Caveat emptor
 ️
Manual task management in the shell, if not done correctly, may cause system instability, especially if you lose track
of high-load jobs. Consider using better solutions, such as automation software, or parallel processing.

## tmux

Run the following commands to launch the job.

```sh
tmux
ssh <to-the-remote-server>
<run-the-job>
```

Press `Ctrl+b` and then `d`. This will close `tmux`, but your remote job will keep running in the background.

Run `tmux a` and you'll see your background job still printing to the console. You can still type commands,
but it will be annoyingly interrupted by the background job. You can do the following:

- Run `fg` to reattach your SSH session to the job.
- Once attached, press `Ctrl+z` and then run `bg` to resume it in the background.
- Run `kill %1` to kill the background job.

`fg` is a shortcut to `fg %1`. The `%1` corresponds to an item in the job list, which you can inspect with `jobs`.
For example, if the snippet below is the current job list, `fg %2` will attach to the second job (`<some-other-job`>).

```sh
$ jobs
[1]+  Running               <run-the-job>
[2]+  Running               <some-other-job>
```

💡 **Note**: `tmux` is the only example in this entire article that allows you to reattach to your job if
you leave your session. This is also useful if you find yourself in a situation where your connection to a remote
server is unreliable, or have a critical workload that cannot be interrupted.

## nohup

Run the following commands to launch the job.

```sh
ssh <to-the-remote-server>
nohup <run-the-job> &
```

The job will run in the background. To view its output live, run `tail -f ~/nohup.out`
(or what the `nohup` command prints after you run it). Press `fg` to reattach. Unlike `tmux`, once you leave your
SSH session, you cannot run `fg` and `kill %1` after reconnecting. The job's process gets orphaned; you need to find
your job's process id and terminate it.

Run the following command to find the process based on the command you wrote in the terminal.
For example, you called `download-something`, which will take a long time to finish.

```sh
ps -ef | grep [d]ownload-something
```

This `grep` trick ensures you don't include the same `grep` command. For example, if the previous command has the
following output, it will report the process id in the second column.

```sh
<you>     12345    5555  0  00:00 pts/0     00:12:34 download-something --arg xyz 
```

Following this example, you can then terminate the orphaned job with `kill 12345`.

## disown

Run the following commands to launch the job.

```sh
ssh <to-the-remote-server>
<run-the-job> & disown
```

This is almost similar to `nohup` but with the following differences:

- The job will print to the console instead of a file. When you leave your SSH session and join back, you will
  never see the command's output again.
- `disown` may not be available in most Linux shells, while `nohup` is a POSIX standard.

## References

- {% include external-link.html url="https://linuxhandbook.com/jobs-command/" %}
