# Squid Proxy Terraform module

## Module overview

### Description

This module will create a VSI with a HTTP/HTTPS proxy (based on squid) installed. The module depends on a pre-existing VPC and subnet to use for the deployment. After the VSI is created, it will be customized to accept traffic to proxy from a specific subnet set in the `allow_network` variable. The module defaults this to be the network `10.0.0.0/8` which may be acceptable for general IBM Cloud VPC use. If the deployment subnet does not have a public gateway, set the `create_public_ip` variable to *true* in order to allow the proxy to reach the Internet.

When deployed in a VPC with multiple subnets/zones, this module will create a VM with a squid proxy for each zone and then configure a VPC Load Balancer to front-end the proxy instances. When the LB is created, instead of a single IP address of the proxy, the hostname for the LB will be provided in the module output.

### Software dependencies

The module depends on the following software components:

#### Command-line tools

- terraform >= v0.15

#### Terraform providers

- IBM Cloud provider >= 1.17

### Module dependencies

This module makes use of the output from other modules:

- VPC - github.com/cloud-native-toolkit/terraform-ibm-vpc.git
- Subnet - github.com/cloud-native-toolkit/terraform-ibm-vpc.git
- KMS - github.com/cloud-native-toolkit/terraform-ibm-kms.git

### Example usage

[Refer test cases for more details](test/stages/stage2-proxy.tf)

```hcl-terraform
module "proxy" {
  source = "github.com/cloud-native-toolkit/terraform-vsi-proxy.git"

  resource_group_name    = module.resource_group.name
  region                 = var.region
  ibmcloud_api_key       = var.ibmcloud_api_key
  vpc_name               = module.vpc.name
  vpc_subnet_count       = module.subnets.count
  vpc_subnets            = module.subnets.subnets
  ssh_key_id             = module.vpcssh.id
}
```
