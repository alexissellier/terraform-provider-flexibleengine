---
subcategory: "Deprecated"
---

# Data Source: flexibleengine_vpc_route_ids_v2

!> **WARNING:** It has been deprecated, use `flexibleengine_vpc_route_table` to get the route details.

`flexibleengine_vpc_route_ids_v2` provides a list of route ids for a vpc_id.

This resource can be useful for getting back a list of route ids for a vpc.

## Example Usage

 ```hcl
 variable "vpc_id" { }

data "flexibleengine_vpc_route_ids_v2" "example" {
  vpc_id = "${var.vpc_id}"
}

data "flexibleengine_vpc_route_v2" "vpc_route" {
  count = "${length(data.flexibleengine_vpc_route_ids_v2.example.ids)}"
  id = "${data.flexibleengine_vpc_route_ids_v2.example.ids[count.index]}"
}

output "route_nexthop" {
  value = ["${data.flexibleengine_vpc_route_v2.vpc_route.*.nexthop}"]
}
 ```

## Argument Reference

* `vpc_id` (Required) - The VPC ID that you want to filter from.

## Attributes Reference

* `ids` - A list of all the route ids found. This data source will fail if none are found.
