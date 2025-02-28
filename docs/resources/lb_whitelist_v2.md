---
subcategory: "Elastic Load Balance (ELB)"
description: ""
page_title: "flexibleengine_lb_whitelist_v2"
---

# flexibleengine_lb_whitelist_v2

Manages an **enhanced** load balancer whitelist resource within FlexibleEngine.

## Example Usage

```hcl
resource "flexibleengine_lb_whitelist_v2" "whitelist_1" {
  enable_whitelist = true
  whitelist        = "192.168.11.1,192.168.0.1/24,192.168.201.18/8"
  listener_id      = "d9415786-5f1a-428b-b35f-2f1523e146d2"
}
```

## Argument Reference

The following arguments are supported:

* `listener_id` - (Required) The Listener ID that the whitelist will be associated with.
  Changing this creates a new whitelist.

* `enable_whitelist` - (Optional) Specify whether to enable access control.

* `whitelist` - (Optional) Specifies the IP addresses in the whitelist. Use commas(,) to separate
  the multiple IP addresses.

* `tenant_id` - (Optional) The UUID of the tenant who owns the whitelist.
  Only administrative users can specify a tenant UUID other than their own.
  Changing this creates a new whitelist.

## Attributes Reference

The following attributes are exported:

* `id` - The unique ID for the whitelist.
* `tenant_id` - See Argument Reference above.
* `listener_id` - See Argument Reference above.
* `enable_whitelist` - See Argument Reference above.
* `whitelist` - See Argument Reference above.
