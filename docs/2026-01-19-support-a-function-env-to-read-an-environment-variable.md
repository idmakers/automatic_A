---
title: "Supporting the `env()` Function in Terraform"
tags:
  - Terraform
  - Hashicorp
  - Environmental Variables
---

# Supporting the `env()` Function in Terraform

## Core Problem

Terraform users often need to access environment variables, such as workload identity tokens, in their configurations. However, Terraform does not currently provide a built-in function to read these environment variables.

## Solution & Analysis

### Proposal

Support a 1st class function: `env()`, similar to [https://developer.hashicorp.com/packer/docs/templates/hcl_templates/functions/contextual/env](https://developer.hashicorp.com/packer/docs/templates/hcl_templates/functions/contextual/env). This function would allow users to easily access environment variables in their Terraform configurations.

### Code Example

```hcl
resource "aws_instance" "example" {
  // Use the new env() function to access the TFC_WORKLOAD_IDENTITY_TOKEN variable
  iam_instance_profile = aws_iam_instance_profile.example.name
  user_data = templatefile("user_data.tmpl", { TFC_WORKLOAD_IDENTITY_TOKEN = env("TFC_WORKLOAD_IDENTITY_TOKEN") })
}

// Define a custom function using the new env() function
func env(key string) string {
  return var.value[key]
}
```

### Explanation

The `env()` function allows users to access environment variables in their Terraform configurations. The `aws_instance` resource example demonstrates how to use this function to access the `TFC_WORKLOAD_IDENTITY_TOKEN` variable.

## Conclusion

Adding support for the `env()` function would greatly improve the usability of Terraform and make it easier for users to access environment variables in their configurations. This feature is essential for users who need to access workload identity tokens or other environment variables in their Terraform deployments.

## Reference
- [Source](https://github.com/hashicorp/terraform/issues/38043)