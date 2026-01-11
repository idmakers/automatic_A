---
title: "Support for `env()` Function in Terraform"
tags:
  - terraform
  - environment-variables
---

# Support for `env()` Function in Terraform

## Core Problem

In Terraform, manually generated workload identity tokens from TFC (Terraform Cloud) are not easily accessible through environment variables. This limitation makes it challenging to implement custom authentication workflows or authenticate with providers that are not natively supported by dynamic credentials.

## Solution & Analysis

The current solution involves using the `data.external` resource in Terraform, which allows you to execute an external program and retrieve its output as a value. In this case, we can use `jq` to extract the value of the `TFC_WORKLOAD_IDENTITY_TOKEN` environment variable from the `env` module.

Here is an example code snippet:
```terraform
data "external" "env" {
  program = ["jq", "-n", "{TFC_WORKLOAD_IDENTITY_TOKEN: env.TFC_WORKLOAD_IDENTITY_TOKEN}"]
}

provider "azuredevops" {
  org_service_url = "https://dev.azure.com/${var.organization}"

  use_oidc = var.ado_use_oidc
  oidc_token    = data.external.env.result.TFC_WORKLOAD_IDENTITY_TOKEN
  client_id = var.ado_client_id
  tenant_id = var.ado_tenant_id
}
```
Alternatively, we can consider using a third-party provider that exposes a function to read an environment variable.

## Conclusion

Introducing a `env()` function in Terraform would provide a more elegant and efficient way to access environment variables, especially when working with workload identity tokens or other dynamic credentials. This feature would simplify the implementation of custom authentication workflows and enhance the overall terraform experience.

## Reference
- [Source](https://github.com/hashicorp/terraform/issues/38043)