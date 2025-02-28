---
subcategory: "Identity and Access Management (IAM)"
description: ""
page_title: "flexibleengine_identity_group_v3"
---

# flexibleengine_identity_group_v3

Manages a User Group resource within FlexibleEngine IAM service.

-> You *must* have admin privileges in your FlexibleEngine cloud to use this resource.

## Example Usage

```hcl
resource "flexibleengine_identity_group_v3" "group_1" {
  name        = "group_1"
  description = "This is a test group"
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the group. The length is less than or equal to 64 bytes.

* `description` - (Optional) A description of the group.

* `domain_id` - (Optional) The domain this group belongs to.

## Attributes Reference

The following attributes are exported:

* `domain_id` - See Argument Reference above.

## Import

Groups can be imported using the `id`, e.g.

```shell
terraform import flexibleengine_identity_group_v3.group_1 89c60255-9bd6-460c-822a-e2b959ede9d2
```
