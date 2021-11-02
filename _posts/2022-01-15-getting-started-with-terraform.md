---
title: "Getting Started With Terraform (IAS)"
categories:
  - Devops
tags:
  - Devops
  - Terraform
  - IAS
---

Terraform is an open-source infrastructure as code software tool.
Infrastructure as code (IaC) tools allow you to manage infrastructure with configuration files rather than through a graphical user interface.

Terraform Providers are plugins that implement resource types:
Ex. azurerm ( hashicorp/terraform-provider-azurerm)
<https://registry.terraform.io/browse/providers>

## Installation

choco install terraform
terraform -install-autocomplete

```javascript
terraform {
    required_providers {
    docker = {
      source = "kreuzwerker/docker"
      version = "~> 2.15.0"
    }
  }
}

provider "docker" {
  host = "npipe:////.//pipe//docker_engine"
}

resource "docker_image" "nginx" {
  name = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name = "tutorial"
  ports {
    internal = 80
    external = 8000
  }
}
```

##

Downloads a plugin that allows Terraform to interact with provider (ex. Docker).

```bash
terraform init
```

Provision resources (Ex. NGINX server container)

```bash
terraform apply
```

Stop the container and destroy the resources

```bash
terraform destroy
```

## Azure Provider

Configure the Azure provider

1/
main.tf

```bash
# Terraform settings, including the required providers
# Terraform will use to provision your infrastructure.

terraform {
  required_providers {
  azurerm = {
    source = "hashicorp/azurerm"
    version = "~> 2.65"
  }
}

required_version = ">= 1.1.0"
}

# The provider block configures the specified provider
provider "azurerm" {
  features {}
}

# define components of your infrastructure.
resource "azurerm_resource_group" "rg" {
  name = var.resource_group_name
  location = "westus2"
}
```

variables.tf

```bash
  variable "resource_group_name" {
  default = "myTFResourceGroup"
}
```

outputs.tf

```bash
  output "resource_group_id" {
  value = azurerm_resource_group.rg.id
}
```

Format document

```bash
terraform fmt
```

Validate document

```bash
terraform validate
```

Apply:

```bash
az login
terraform apply
```

When you apply your configuration, Terraform writes data into a file called terraform.tfstate

# Inspect the current state

terraform show
terraform state list

# Inspect the current state

terraform show

terraform state
terraform state list

```

```
