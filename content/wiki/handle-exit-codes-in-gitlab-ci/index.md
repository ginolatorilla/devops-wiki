---
date: 2024-06-23
title: Handle Exit Codes in GitLab CI Jobs
tags: [gitlab, ci]
---

When a command in a GitLab CI job _fails_ (i.e., exits with a non-zero code), the job script stops running and fails.
For example, `grep` exits with 1 when it doesn't find a match or with >1 for actual errors.

```yaml
a job that calls grep:
  script:
    - ./do_some_stuff.sh
    - input=$(grep -o 'some-expected-pattern' in-a-file-created.previously)
    # This won't execute if the grep in the previous line doesn't find a match.
    - ./do_some_other_stuff.sh $input
```

Here's an example of how you should handle it:

```yaml
a job that calls grep:
  script:
    - ./do_some_stuff.sh
    - input=$(grep -o 'some-expected-pattern' in-a-file-created.previously) || exit_code=$?
    - if [ $exit_code -gt 1 ]; then exit $exit_code; fi
    - ./do_some_other_stuff.sh $input
```

You need to add a trailing `|| exit_code=$?` at the end of the line where you call the non-zero exiting command---
in this case, it's `grep`.

- `||` runs the script if the left side has a non-zero exit code.
- `$?` is the exit code of the last command that the shell executed. The shell will always consider assignments to
    environment variables as successful.

If the exit code represents an error, the `if` statement is there so you can abort the job.

## Using allow_failure for visuals

There are situations where visual feedback in the pipeline can be handy. Take this example with `terraform`.

```yaml
terraform plan:
  script:
    - terraform plan
    - do-some-other-stuff.sh
```

The job will be successful, but you must inspect it to see if there are actual changes. Pass the command line argument
`-detailed-exitcode` so that exit code 2 means there will be changes. Use this with the error-handling job logic
so the job won't fail entirely.

```yaml
terraform plan:
  script:
    - terraform plan -detailed-exitcode || exit_code=$?
    - if [ $exit_code -eq 1 ]; then exit $exit_code; fi
    - do-some-other-stuff.sh
    - exit $exit_code
  allow_failure:
    exit_codes:
      - 2
```

The final `exit` command here will use the one by the `terraform` command. The `allow_failure.exit_codes` block
will make GitLab treat that error code as successful and paint the job yellow in the pipeline. It gives you
a simple indicator that the `terraform` job will propose changes, which scales well if there are more jobs.

You can see this in action with `grep` in this [GitLab CI job](https://gitlab.com/ginolatorilla/gitlab-ci-recipes/-/jobs/7166047999).

Jobs that are allowed to fail will look like this:

![GitLab CI failed job with `allow_failure`](ci-job-with-allow-failure.png)

## References

- <https://docs.gitlab.com/ee/ci/yaml/script.html#ignore-non-zero-exit-codes>
- <https://docs.gitlab.com/ee/ci/yaml/#allow_failureexit_codes>
