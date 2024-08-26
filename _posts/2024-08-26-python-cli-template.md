---
layout: post
title: Python CLI Template
tags: python cli template
---

Use these templates if you need to build single-file Python CLIs without external packages.
Replace placeholders indicated by `${TODO:...}` or `# TODO: ...`.

## Simple program

```python
#!/usr/bin/env python3
"""${TODO: app_name} -- ${TODO: short description}
${TODO: detailed description}
${TODO: copyright notice}
"""
import argparse


def main():
    opts = get_program_options()
    # TODO: Handle program options here


def get_program_options():
    class _FC(argparse.RawDescriptionHelpFormatter,
              argparse.ArgumentDefaultsHelpFormatter):
        pass

    parser = argparse.ArgumentParser(description=__doc__, formatter_class=_FC)
    # TODO: Add command line arguments here that will be parsed during runtime.
    parser.add_argument("input", help="Required input")
    parser.add_argument("-o",
                        "--option",
                        help="Optional input",
                        default="foobarbaz")
    parser.add_argument("-f",
                        "--flag",
                        help="Toggle flag",
                        action="store_true")
    return parser.parse_args()


if __name__ == "__main__":
    main()
```

## Subcommands

```python
#!/usr/bin/env python3
"""${TODO: app_name} -- ${TODO: short description}
${TODO: detailed description}
${TODO: copyright notice}
"""
import argparse


def main():
    opts = get_program_options()
    opts.func(None)


def subcommand_1_main(args):
    # TODO: Handle program options here
    pass


def subcommand_2_main(args):
    # TODO: Handle program options here
    pass


def get_program_options():
    class _FC(argparse.RawDescriptionHelpFormatter,
              argparse.ArgumentDefaultsHelpFormatter):
        pass

    parser = argparse.ArgumentParser(description=__doc__, formatter_class=_FC)
    parser.set_defaults(func=lambda args: parser.print_help())

    # TODO: Add command line arguments here that will be parsed during runtime.
    # These arguments are common to all sub-commands.
    parser.add_argument("-o",
                        "--option",
                        help="Optional input",
                        default="foobarbaz")
    parser.add_argument("-f",
                        "--flag",
                        help="Toggle flag",
                        action="store_true")

    # TODO: Add subparsers here (ie for sub-commands), or add subcommand-specific
    # arguments.
    sp = parser.add_subparsers(
        description="Run with -h or --help to view sub-command help.")

    psc1 = sp.add_parser("subcommand_1",
                         description="Subcommand 1 does something.")
    psc1.add_argument("input", help="Required input")
    psc1.set_defaults(func=subcommand_1_main)

    psc2 = sp.add_parser("subcommand_2",
                         description="Subcommand 2 does something.")
    psc2.add_argument("input", help="Required input")
    psc2.set_defaults(func=subcommand_2_main)

    return parser.parse_args()


if __name__ == "__main__":
    main()
```
