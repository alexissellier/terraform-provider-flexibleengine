---
subcategory: "Auto Scaling (AS)"
description: ""
page_title: "flexibleengine_as_group"
---

# flexibleengine_as_group

Manages a V1 Autoscaling Group resource within flexibleengine.

## Example Usage

### Basic Autoscaling Group

```hcl
resource "flexibleengine_vpc_v1" "example_vpc" {
  name = "example-vpc"
  cidr = "192.168.0.0/16"
}

resource "flexibleengine_vpc_subnet_v1" "example_subnet" {
  name       = "example-vpc-subnet"
  cidr       = "192.168.0.0/24"
  gateway_ip = "192.168.0.1"
  vpc_id     = flexibleengine_vpc_v1.example_vpc.id
}

resource "flexibleengine_as_group" "my_as_group" {
  scaling_group_name       = "my_as_group"
  desire_instance_number   = 2
  min_instance_number      = 0
  max_instance_number      = 10
  scaling_configuration_id = "37e310f5-db9d-446e-9135-c625f9c2bbfc"
  vpc_id                   = flexibleengine_vpc_v1.example_vpc.id
  delete_publicip          = true
  delete_instances         = "yes"

  networks {
    id = flexibleengine_vpc_subnet_v1.example_subnet.id
  }
  security_groups {
    id = "45e4c6de-6bf0-4843-8953-2babde3d4810"
  }
}
```

### Autoscaling Group Only Remove Members When Scaling Down

```hcl
resource "flexibleengine_vpc_v1" "example_vpc" {
  name = "example-vpc"
  cidr = "192.168.0.0/16"
}

resource "flexibleengine_vpc_subnet_v1" "example_subnet" {
  name       = "example-vpc-subnet"
  cidr       = "192.168.0.0/24"
  gateway_ip = "192.168.0.1"
  vpc_id     = flexibleengine_vpc_v1.example_vpc.id
}

resource "flexibleengine_as_group" "my_as_group_only_remove_members" {
  scaling_group_name       = "my_as_group_only_remove_members"
  desire_instance_number   = 2
  min_instance_number      = 0
  max_instance_number      = 10
  scaling_configuration_id = "37e310f5-db9d-446e-9135-c625f9c2bbfc"
  vpc_id                   = flexibleengine_vpc_v1.example_vpc.id
  delete_publicip          = true
  delete_instances         = "no"

  networks {
    id = flexibleengine_vpc_subnet_v1.example_subnet.id
  }
  security_groups {
    id = "45e4c6de-6bf0-4843-8953-2babde3d4810"
  }
}
```

### Autoscaling Group With ELB Listener

```hcl
resource "flexibleengine_vpc_v1" "example_vpc" {
  name = "example-vpc"
  cidr = "192.168.0.0/16"
}

resource "flexibleengine_vpc_subnet_v1" "example_subnet" {
  name       = "example-vpc-subnet"
  cidr       = "192.168.0.0/24"
  gateway_ip = "192.168.0.1"
  vpc_id     = flexibleengine_vpc_v1.example_vpc.id
}

resource "flexibleengine_lb_loadbalancer_v2" "lb_1" {
  vip_subnet_id = flexibleengine_vpc_subnet_v1.example_subnet.ipv4_subnet_id
}

resource "flexibleengine_lb_listener_v2" "listener_1" {
  protocol        = "HTTP"
  protocol_port   = 8080
  loadbalancer_id = flexibleengine_lb_loadbalancer_v2.lb_1.id
}

resource "flexibleengine_as_group" "my_as_group_with_elb" {
  scaling_group_name       = "my_as_group_with_elb"
  desire_instance_number   = 2
  min_instance_number      = 0
  max_instance_number      = 10
  lb_listener_id           = flexibleengine_lb_listener_v2.listener_1.id
  scaling_configuration_id = "37e310f5-db9d-446e-9135-c625f9c2bbfc"
  vpc_id                   = flexibleengine_vpc_v1.example_vpc.id
  delete_publicip          = true
  delete_instances         = "yes"

  networks {
    id = flexibleengine_vpc_subnet_v1.example_subnet.id
  }
  security_groups {
    id = "45e4c6de-6bf0-4843-8953-2babde3d4810"
  }
}
```

## Argument Reference

The following arguments are supported:

* `region` - (Optional) The region in which to create the AS group. If
    omitted, the `region` argument of the provider is used. Changing this
    creates a new AS group.

* `scaling_group_name` - (Required) The name of the scaling group. The name can contain letters,
    digits, underscores(_), and hyphens(-),and cannot exceed 64 characters.

* `scaling_configuration_id` - (Optional) The configuration ID which defines
    configurations of instances in the AS group.

* `desire_instance_number` - (Optional) The expected number of instances. The default
    value is the minimum number of instances. The value ranges from the minimum number of
    instances to the maximum number of instances.

* `min_instance_number` - (Optional) The minimum number of instances.
    The default value is 0.

* `max_instance_number` - (Optional) The maximum number of instances.
    The default value is 0.

* `cool_down_time` - (Optional) The cooling duration (in seconds). The value ranges
    from 0 to 86400, and is 900 by default.

* `lb_listener_id` - (Optional) The ELB listener IDs. The system supports up to
    six ELB listeners, the IDs of which are separated using a comma (,).

* `lbaas_listeners` - (Optional) An array of one or more enhanced load balancer.
    The system supports the binding of up to six load balancers. The field is
    alternative to `lb_listener_id`.  The object structure is documented below.

* `available_zones` - (Optional) The availability zones in which to create
    the instances in the autoscaling group.

* `vpc_id` - (Required) The VPC ID. Changing this creates a new group.

* `networks` - (Required) An array of one or more network IDs.
    The system supports up to five networks. The networks object structure
    is documented below.

* `security_groups` - (Required) An array of `one` security group ID to
    associate with the group. The security_groups object structure is
    documented below.

* `health_periodic_audit_method` - (Optional) The health check method for instances
    in the AS group. The health check methods include `ELB_AUDIT` and `NOVA_AUDIT`.
    If load balancing is configured, the default value of this parameter is `ELB_AUDIT`.
    Otherwise, the default value is `NOVA_AUDIT`.

* `health_periodic_audit_time` - (Optional) The health check period for instances.
    The period has four options: 5 minutes (default), 15 minutes, 60 minutes, and 180 minutes.

* `instance_terminate_policy` - (Optional) The instance removal policy. The policy has
    four options: `OLD_CONFIG_OLD_INSTANCE` (default), `OLD_CONFIG_NEW_INSTANCE`,
    `OLD_INSTANCE`, and `NEW_INSTANCE`.

* `tags` - (Optional, Map) The key/value pairs to associate with the scaling group.

* `notifications` - (Optional) The notification mode. The system only supports `EMAIL`
    mode which refers to notification by email.

* `delete_publicip` - (Optional) Whether to delete the elastic IP address bound to the
    instances of AS group when deleting the instances. The options are `true` and `false`.

* `delete_instances` - (Optional) Whether to delete the instances in the AS group
    when deleting the AS group. The options are `yes` and `no`.

* `force_delete` - (Optional) Whether to forcibly delete the AS group, remove the ECS instances and release them.
  The default value is `false`.

The `networks` block supports:

* `id` - (Required) The network UUID.

The `security_groups` block supports:

* `id` - (Required) The UUID of the security group.

The `lbaas_listeners` block supports:

* `pool_id` - (Required) Specifies the backend ECS group ID.
* `protocol_port` - (Required) Specifies the backend protocol, which is the port on which
  a backend ECS listens for traffic. The number of the port ranges from 1 to 65535.
* `weight` - (Optional) Specifies the weight, which determines the portion of requests a
  backend ECS processes compared to other backend ECSs added to the same listener. The value
  of this parameter ranges from 0 to 100. The default value is 1.

## Attributes Reference

In addition to all arguments above, the following attributes are exported:

* `status` - Indicates the status of the AS group.
* `instances` - The instances IDs of the AS group.
* `current_instance_number` - Indicates the number of current instances in the AS group.
