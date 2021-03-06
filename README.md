# Azure - Kubernetes Module

## Introduction

This module will create a managed Kubernetes cluster using Azure Kubernetes Service.
<br />

<!--- BEGIN_TF_DOCS --->
## Providers

| Name | Version |
|------|---------|
| azurerm | >= 2.48.0 |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:-----:|
| acr\_pull\_access | map of ACR ids to allow AcrPull | `map(string)` | `{}` | no |
| cluster\_name | Name of AKS cluster. | `string` | n/a | yes |
| configure\_network\_role | Add Network Contributor role for identity on input subnets. | `bool` | `true` | no |
| configure\_subnet\_nsg\_rules | Configure required AKS NSG rules on input subnets. | `bool` | `true` | no |
| custom\_route\_table\_ids | Custom route tables used by node pool subnets. | `map(string)` | `{}` | no |
| default\_node\_pool | Default node pool.  Value refers to key within node\_pools variable. | `string` | `"default"` | no |
| dns\_prefix | DNS prefix specified when creating the managed cluster. | `string` | n/a | yes |
| enable\_kube\_dashboard | enable kubernetes dashboard | `bool` | `false` | no |
| identity\_type | SystemAssigned or UserAssigned. | `string` | `"UserAssigned"` | no |
| kubernetes\_version | kubernetes version | `string` | n/a | yes |
| location | Azure region. | `string` | n/a | yes |
| names | Names to be applied to resources. | `map(string)` | n/a | yes |
| network\_plugin | network plugin to use for networking (azure or kubenet) | `string` | `"kubenet"` | no |
| node\_pool\_defaults | node pool defaults | <pre>object({<br>                  vm_size                            = string<br>                  availability_zones                 = list(number)<br>                  node_count                         = number<br>                  enable_auto_scaling                = bool<br>                  min_count                          = number<br>                  max_count                          = number<br>                  enable_host_encryption             = bool<br>                  enable_node_public_ip              = bool<br>                  max_pods                           = number<br>                  node_labels                        = map(string)<br>                  only_critical_addons_enabled       = bool<br>                  orchestrator_version               = string<br>                  os_disk_size_gb                    = number<br>                  os_disk_type                       = string<br>                  type                               = string<br>                  tags                               = map(string)<br>                  subnet                             = string # must be key from node_pool_subnets variable<br><br>                  # settings below not available in default node pools<br>                  mode                               = string<br>                  node_taints                        = list(string)<br>                  max_surge                          = string<br>                  eviction_policy                    = string<br>                  os_type                            = string<br>                  priority                           = string<br>                  proximity_placement_group_id       = string<br>                  spot_max_price                     = number<br>  })</pre> | <pre>{<br>  "availability_zones": [<br>    1,<br>    2,<br>    3<br>  ],<br>  "enable_auto_scaling": false,<br>  "enable_host_encryption": false,<br>  "enable_node_public_ip": false,<br>  "eviction_policy": null,<br>  "max_count": null,<br>  "max_pods": null,<br>  "max_surge": "1",<br>  "min_count": null,<br>  "mode": "User",<br>  "name": null,<br>  "node_count": 1,<br>  "node_labels": null,<br>  "node_taints": null,<br>  "only_critical_addons_enabled": false,<br>  "orchestrator_version": null,<br>  "os_disk_size_gb": null,<br>  "os_disk_type": "Managed",<br>  "os_type": "Linux",<br>  "priority": "Regular",<br>  "proximity_placement_group_id": null,<br>  "spot_max_price": null,<br>  "subnet": null,<br>  "tags": null,<br>  "type": "VirtualMachineScaleSets",<br>  "vm_size": "Standard_B2s"<br>}</pre> | no |
| node\_pool\_subnets | Subnet info used with node\_pools variable. | <pre>map(object({<br>                  id                          = string # subnet_id<br>                  resource_group_name         = string # resource group containing virtual_network/subnets<br>                  network_security_group_name = string # network_security_group name associated with subnet<br>                }))</pre> | `{}` | no |
| node\_pools | node pools | `any` | <pre>{<br>  "default": {}<br>}</pre> | no |
| node\_resource\_group | The name of the Resource Group where the Kubernetes Nodes should exist. | `string` | n/a | yes |
| rbac | role based access control settings | <pre>object({<br>                  enabled        = bool<br>                  ad_integration = bool<br>                })</pre> | <pre>{<br>  "ad_integration": false,<br>  "enabled": true<br>}</pre> | no |
| rbac\_admin\_object\_ids | Admin group object ids for use with rbac active directory integration | `map(string)` | `{}` | no |
| resource\_group\_name | Resource group name. | `string` | n/a | yes |
| subnet\_nsg\_rule\_priority\_start | Starting point for NSG rulee priorities. | `number` | `1000` | no |
| tags | Tags to be applied to resources. | `map(string)` | n/a | yes |
| user\_assigned\_identity | User assigned identity for the manged cluster (leave and the module will create one). | <pre>object({<br>                  id           = string<br>                  principal_id = string<br>                  client_id    = string<br>                })</pre> | n/a | yes |
| windows\_profile | windows profile admin user/pass | <pre>object({ <br>                  admin_username = string<br>                  admin_password = string<br>                })</pre> | n/a | yes |

## Outputs

| Name | Description |
|------|-------------|
| client\_certificate | kubernetes client certificate |
| client\_key | kubernetes client key |
| cluster\_ca\_certificate | kubernetes cluster ca certificate |
| effective\_outbound\_ips\_ids | The outcome (resource IDs) of the specified arguments. |
| fqdn | kubernetes managed cluster fqdn |
| host | kubernetes host |
| id | kubernetes managed cluster id |
| kube\_config | kubernetes config to be used by kubectl and other compatible tools |
| kube\_config\_raw | raw kubernetes config to be used by kubectl and other compatible tools |
| kubelet\_identity | kubelet identity information |
| name | kubernetes managed cluster name |
| node\_resource\_group | auto-generated resource group which contains the resources for this managed kubernetes cluster |
| password | kubernetes password |
| principal\_id | id of the principal used by this managed kubernetes cluster |
| username | kubernetes username |
<!--- END_TF_DOCS --->

## Example

~~~~
provider "azurerm" {
  version = ">=2.0.0"
  features {}
  subscription_id = "00000-0000-0000-0000-0000000"
}

# Subscription
module "subscription" {
  source = "git@github.com:Azure-Terraform/terraform-azurerm-subscription-data.git?ref=v1.0.0"
}

# Metadata
module "metadata" {
  source = "git@github.com:Azure-Terraform/terraform-azurerm-metadata.git?ref=v1.0.0"

  subscription_id     = module.subscription.output.subscription_id
  # These values should be taken from https://github.com/openrba/python-azure-naming
  business_unit       = "rba.businessUnit"
  cost_center         = "rba.costCenter"
  environment         = "rba.environment"
  location            = "rba.azureRegion"
  market              = "rba.market"
  product_name        = "rba.productName"
  product_group       = "rba.productGroup"
  project             = "project-url"
  sre_team            = "team-name"
  subscription_type   = "rba.subscriptionType"
  resource_group_type = "rba.resourceGroupType"

  additional_tags = {
    "example" = "an additional tag"
  }
}

# Resource group
module "resource_group" {
  source = "git@github.com:Azure-Terraform/terraform-azurerm-resource-group.git?ref=v1.0.0"

  location = module.metadata.location
  tags     = module.metadata.tags
  name     = module.metadata.names
}

# AKS
## This will create a managed kubernetes cluster
module "aks" {
  source = "git@github.com:Azure-Terraform/terraform-azurerm-kubernetes.git"

  service_principal_id     = var.service_principal_id
  service_principal_secret = var.service_principal_secret
  service_principal_name   = "service-principal-name"

  resource_group_name = module.resource_group.name
  location            = module.resource_group.location

  names = module.metadata.names
  tags  = module.metadata.tags

  kubernetes_version = "1.16.7"

  default_node_pool_name                = "default"
  default_node_pool_vm_size             = "Standard_D2s_v3"
  default_node_pool_enable_auto_scaling = true
  default_node_pool_node_min_count      = 1
  default_node_pool_node_max_count      = 5
  default_node_pool_availability_zones  = [1,2,3]

  enable_kube_dashboard = true
  
}

resource "azurerm_kubernetes_cluster_node_pool" "gpu" {
  name                  = "gpu"
  kubernetes_cluster_id = module.aks.id
  vm_size               = "Standard_NC6s_v3"
  availability_zones    = [1,2,3]

  enable_auto_scaling = true
  node_count          = 1
  min_count           = 1
  max_count           = 5

  tags = module.metadata.tags
}

# Helm
provider "helm" {
  alias = "aks"
  kubernetes {
    host                   = module.aks.host
    client_certificate     = base64decode(module.aks.client_certificate)
    client_key             = base64decode(module.aks.client_key)
    cluster_ca_certificate = base64decode(module.aks.cluster_ca_certificate)
  }
}

module "aad-pod-identity" {
  source = "git@github.com:Azure-Terraform/terraform-azurerm-kubernetes.git/aad-pod-identity"
  
  providers = {
    helm = helm.aks
  }

  resource_group_name    = module.resource_group.name
  service_principal_name = "service-principal-name"

  aad_pod_identity_version = "1.6.0"
}
~~~~
