---
title: "Avoiding Sensitive Attributes in Terraform State"
tags:
  - terraform state
  - sensitive attributes
---

# Avoiding Sensitive Attributes in Terraform State

## Core Problem
Terraform state can contain secrets, which is well documented. However, sometimes there are situations where you absolutely don't want Terraform to retain the secret value.

## Solution & Analysis
### Proposing a Setting: `do_not_store`

To address this issue, we propose introducing a setting similar to `sensitive_attributes` that tells TF to store the empty/zero version of an attribute's type in the state and not the actual value used at runtime. This would need to be configurable by the user, similar to `lifecycle.ignore_changes`.

```hcl
resource "vault_generic_secret" "db_password" {
  path = "secret/db"

  data_json = jsonencode({ password = data.vault_generic_secret.random_pw.data.password })
  disable_read = true

  lifecycle {
    ignore_changes = [
      data, data_json
    ]
    do_not_store = [
      data, data_json
    ]
  }
}
```

This will cause config drift for users who enable this and do not have a full grasp of where it is used.

## Conclusion
To ensure that Terraform state doesn't store sensitive attributes, we propose introducing a new setting called `do_not_store`. This setting would allow users to configure which attributes should be excluded from the state, ensuring that only non-sensitive values are stored. However, this raises concerns about how to handle attributes that providers rely on for future operations and how to restrict their use in the configuration.

## Reference
- [Source](https://github.com/hashicorp/terraform/issues/30469)