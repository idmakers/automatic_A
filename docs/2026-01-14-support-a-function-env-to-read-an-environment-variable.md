---
title: "Terraform's `env()` Function: Simplifying Environment Variable Access"
tags:
  - terraform
  - environment-variables
  - automation

---

# Using the `env()` Function in Terraform

## Core Problem

When working with Terraform, managing environment variables can be a challenge. The `TFC_WORKLOAD_IDENTITY_TOKEN` variable is particularly problematic, as it requires manual intervention to access and use its value.

## Solution & Analysis

```hcl
data "external" "env" {
  program = ["jq", "-n", "{TFC_WORKLOAD_IDENTITY_TOKEN: env.TFC_WORKLOAD_IDENTITY_TOKEN}"]
}

provider "example" {
  # ...
  oidc_token = data.external.env.result.TFC_WORKLOAD_IDENTITY_TOKEN
}
```

or

```hcl
data "external" "env" {
  program = ["jq", "-n", "{TFC_WORKLOAD_IDENTITY_TOKEN: env.TFC_WORKLOAD_IDENTITY_TOKEN}"]
}

resource "example" "provider" {
  # ...
  oidc_token = data.external.env.result.TFC_WORKLOAD_IDENTITY_TOKEN
}
```

By introducing a new `data` source, the `env()` function allows you to easily access environment variables in your Terraform configuration. This simplifies the process of managing identity tokens and other sensitive information.

## Conclusion

With the introduction of the `env()` function, Terraform users can now simplify their workflow by leveraging existing environment variable management tools. This feature is particularly useful for automating workflows and integrating with external systems.

Note: The above example uses the `jq` command to parse the JSON output of the `data.external.env` source. This assumes that the value of `TFC_WORKLOAD_IDENTITY_TOKEN` can be safely passed as a string.

## Reference
- [Source](https://github.com/hashicorp/terraform/issues/38043)