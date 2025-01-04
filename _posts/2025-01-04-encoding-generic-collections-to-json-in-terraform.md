---
layout: post
title: Encoding Generic Collections to JSON in Terraform
tags: terraform
---

`jsonencode` is a function that you can use in Terraform that converts objects to JSON-formatted strings.
You might use this function in resources or modules that require JSON strings. For example, AWS CloudWatch
Event Targets require JSON strings in the [`input`](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/cloudwatch_event_target#input-1).

## Issue with type conversions

The issue with `jsonencode` is that it may appear not to properly handle generic collections (e.g. `list(any)` or `map(any)` values).
Consider the following code:

```tf
variable "test" {
  default = [1, "hello"]
}

output "test" {
  value = jsonencode(var.test)
}
```

Here's the output of `terraform apply -auto-approve`:

```plaintext
Changes to Outputs:
  + test = jsonencode(
        [
          + 1,
          + "hello",
        ]
    )

You can apply this plan to save these new output values to the Terraform state, without
changing any real infrastructure.

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

test = "[1,\"hello\"]"
```

It looks fine, as we can clearly see the list didn't change, and the JSON-string is as expected.
However, if we add a type constraint to the input variable, then the output will change:

```tf
variable "test" {
  type    = list(any)
  default = [1, "hello"]
}

output "test" {
  value = jsonencode(var.test)
}
```

Here's the output of `terraform apply -auto-approve`:

```plaintext
Changes to Outputs:
  + test = jsonencode(
        [
          + "1",
          + "hello",
        ]
    )

You can apply this plan to save these new output values to the Terraform state, without
changing any real infrastructure.

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

test = "[\"1\",\"hello\"]"
```

This conversion happens because of two rules:

1. When you constrain a variable into a collection type (i.e. `list` or `map`), it requires all elements to be the same type.
2. Terraform will try to convert each element to `string` values, or fail. For example, one of the elements is a `list`
   which Terraform cannot convert to a string.

## Solutions

Depending on your provider, the conversion of each element to `string` might not be an issue. But if it is, follow these
recommendations.

### Do not use collection functions

Don't use `tolist`, `tomap`, and `zipmap`. These functions will take in `object` and `tuple` (structural) types,
which are unaffected by the type conversion issue.

The following code demonstrates `jsonencode` working with structural types:

```sh
$ echo 'jsonencode([true, 1, "a"])' | terraform console
"[true,1,\"a\"]"

$echo 'jsonencode({a=1, b=[2]})' | terraform console
"{\"a\":1,\"b\":[2]}"
```

### Don't use collection types as variable type constraints

Constrain your input variables to `string`, and use `jsondecode` to check if the input is JSON. *Let the users handle JSON encoding*.
If you look at the AWS provider, inputs that require JSON are string types.

```tf
variable "test" {
  type = string
  validation {
    condition = jsondecode(var.test)
    error_message = "not json"
  }
}
```

## References

- <https://developer.hashicorp.com/terraform/language/expressions/type-constraints#list>
- <https://developer.hashicorp.com/terraform/language/expressions/type-constraints#conversion-of-primitive-types>
- <https://developer.hashicorp.com/terraform/language/expressions/type-constraints#any-with-collection-types>
