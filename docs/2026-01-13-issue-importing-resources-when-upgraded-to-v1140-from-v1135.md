---
title: "Issue Importing Resources When Upgraded to v1.14.0 from v1.13.5"
tags:
  - terraform
  - import
  - resource management
---

# Issue with Importing Resources in Terraform v1.14.0

## Core Problem
When upgrading from Terraform v1.13.5 to v1.14.0, users are experiencing issues importing resources using the `terraform import` command. The error message is not helpful, and it's unclear why this change was introduced.

## Solution & Analysis
The issue is caused by changes in how Terraform handles dynamic values in the `for_each` argument of resource definitions. In v1.14.0, Terraform requires that the map keys used in `for_each` be statically defined in the configuration, rather than being derived from resource attributes.

To fix this issue, you can update your resource definitions to use static map keys or use the `-target planning` option to first apply only the resources that the `for_each` value depends on, and then apply a second time to fully converge.

Here is an example of how to update a resource definition with a static map key:
```terraform
resource "aws_route" "ipv4_private_routes" {
  for_each = var.destination_cidr_block != "" ? { for cidr in var.private_route_table_ids : cidr => cidr } : {}
  # ...
}
```
And here is an example of how to use the `-target planning` option:
```terraform
terraform import -var-file input.json aws_vpc.default vpc-HIDDEN

data.external.git_version: Reading...
# ...

target : aws_route, ipv4_private_routes {
  for_each = var.destination_cidr_block != "" ? { for cidr in var.private_route_table_ids : cidr => cidr } : {}
}
```
## Conclusion
When upgrading to Terraform v1.14.0, be sure to review your resource definitions and update them as necessary to handle dynamic values correctly. The `-target planning` option can be a useful workaround for resources that require dynamic map keys.

## Reference
- [Source](https://github.com/hashicorp/terraform/issues/37938)