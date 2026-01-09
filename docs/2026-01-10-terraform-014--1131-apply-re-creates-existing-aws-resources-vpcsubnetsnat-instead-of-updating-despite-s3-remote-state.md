---

title: "Terraform 0.14 → 1.13.1: apply re-creates existing AWS resources (VPC/Subnets/NAT) instead of updating despite S3 remote state"

tags:
  - Terraform
  - AWS
  - S3
  - Remote State

---

# Upgrading Terraform from v0.14.x to v1.13.1: A Cautionary Tale

## Core Problem
When upgrading from Terraform v0.14.x to v1.13.1, users may notice that `terraform apply` creates entirely new resources instead of updating existing ones, even when using a S3 remote state backend.

## Solution & Analysis
To address this issue, it's essential to understand the changes in Terraform v1.13.1 and how they affect the behavior of `terraform apply`. Specifically, the introduction of a new `terraform.tfstate` file format in v1.10.x has caused some providers to behave differently.

In your case, the AWS provider is affected by this change. To fix the issue, you need to update your Terraform configuration to use the new `terraform.tfstate` file format.

Here's an example of how to modify your `backend` block:
```terraform
data "terraform_remote_state" "state" {
  backend = "s3"
  config = {
    bucket         = var.backend_config_bucket
    region         = var.backend_config_bucket_region
    key            = "${var.name}/${var.backend_config_tfstate_file_key}"
    dynamodb_table = "terraform_locks"
    encrypt        = true
  }
}
```
Make sure to update the `key` field to use the new `terraform.tfstate` file format.

Additionally, you may need to modify your `terraform.tfstate.discard` block to ensure that Terraform properly discards old state files:
```terraform
terraform {
  # ... other settings ...
  tfstate_discard = true
}
```
## Conclusion
When upgrading from Terraform v0.14.x to v1.13.1, it's essential to understand the changes in the provider behavior and update your configuration accordingly. By using the new `terraform.tfstate` file format and modifying your `backend` block, you can ensure that `terraform apply` updates existing resources instead of creating duplicates.

**Code Examples**

```terraform
data "terraform_remote_state" "state" {
  backend = "s3"
  config = {
    bucket         = var.backend_config_bucket
    region         = var.backend_config_bucket_region
    key            = "${var.name}/${var.backend_config_tfstate_file_key}"
    dynamodb_table = "terraform_locks"
    encrypt        = true
  }
}

terraform {
  tfstate_discard = true
}
```

```terraform
provider "aws" {
  region = var.region
  assume_role {
    role_arn = "arn:aws:iam::${var.target_account_id}:role/terraform"
  }
}
```
Note: The code examples provided are for illustration purposes only and may need to be modified to fit your specific use case.

## Reference
- [Source](https://github.com/hashicorp/terraform/issues/37536)