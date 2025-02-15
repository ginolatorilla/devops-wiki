---
date: 2024-08-07
title: Optional Configuration Blocks in Terraform
tags: [terraform]
---

A [Terraform dynamic block](https://developer.hashicorp.com/terraform/language/expressions/dynamic-blocks)
lets you add configuration blocks to resources. Depending on a sequence of values, you can add as many
configuration blocks as you like, even wholly omitting them.

The `for_each` argument is the primary driver of dynamic blocks. Because of this, we need to write extra code for a
simple use case, such as an optional block. To illustrate, we desire the effect of a flag argument, which is
unsupported by Terraform:

```hcl
# Desired
dynamic "config_block" {
    create = local.create_config
    content {
        ...
    }
}

# Actual
dynamic "config_block" {
    for_each = local.create_config? [1] : []
    content {
        ...
    }
}
```

Fortunately, Terraform has a feature to support such behaviour with **splat expressions**. They even
[documented](https://developer.hashicorp.com/terraform/language/expressions/splat#single-values-as-lists)
that feature. This code is equivalent to the previous illustrations and is much cleaner.

```hcl
dynamic "config_block" {
    for_each = local.create_config[*]
    content {
        ...
    }
}
```

However, there's one catch: the control variable must be _nullable_ (i.e., it can be `null` or any other type).
There are many ways to generate nullables. Here are some examples:

- Call the [try](https://developer.hashicorp.com/terraform/language/functions/try) function

  ```hcl
  locals {
    create_config = try(..., null)
  }
  ```

- Declare one of the attributes of an input variable with an `object` type to `optional`

  ```hcl
  var "config" {
    type = object({
      maybe_id = optional(string)
    })
  }

  locals {
    create_config = var.config.maybe_id
  }
  ```

## References

- <https://developer.hashicorp.com/terraform/language/expressions/type-constraints#optional-object-type-attributes>
