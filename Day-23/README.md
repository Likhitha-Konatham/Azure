# Terraform Azure Virtual Machine with Remote Backend

This project demonstrates how to provision a **Linux Virtual Machine on Microsoft Azure** using **Terraform**, along with networking components and a **remote backend** stored in Azure Storage Account.

---

## Project Structure

```bash
.
├── main.tf
├── backend.tf
└── README.md

Prerequisites:
- Azure Subscription
- Azure CLI
- Terraform (v1.x)

SSH Public Key available at:
~/.ssh/id_rsa.pub

Azure Storage Account (for Terraform backend):

Azure Login:
az login

(Optional) Set subscription:
az account set --subscription "<SUBSCRIPTION_ID>"

main.tf

terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "=3.0.0"
    }
  }
}

provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources1"
  location = "EAST US"
}

resource "azurerm_virtual_network" "example" {
  name                = "example-network"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
}

resource "azurerm_subnet" "example" {
  name                 = "internal"
  resource_group_name  = azurerm_resource_group.example.name
  virtual_network_name = azurerm_virtual_network.example.name
  address_prefixes     = ["10.0.2.0/24"]
}

resource "azurerm_public_ip" "example" {
  name                = "example_pip"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  allocation_method   = "Dynamic"
}

resource "azurerm_network_interface" "example" {
  name                = "example-nic"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.example.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.example.id
  }
}

resource "azurerm_linux_virtual_machine" "example" {
  name                = "example-machine"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  size                = "Standard_F2"
  admin_username      = "adminuser"

  network_interface_ids = [
    azurerm_network_interface.example.id,
  ]

  admin_ssh_key {
    username   = "adminuser"
    public_key = file("~/.ssh/id_rsa.pub")
  }

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }

  source_image_reference {
    publisher = "Canonical"
    offer     = "0001-com-ubuntu-server-jammy"
    sku       = "22_04-lts"
    version   = "latest"
  }
}

backend.tf (Remote State):

terraform {
  backend "azurerm" {
    storage_account_name = "azurebackendstorageacct"
    container_name       = "backend"
    key                  = "terraform.tfstate"
    access_key           = ""
  }
}

Ensure the Storage Account and container exist before running terraform init.

Terraform Commands:
Initialize Terraform:
terraform init

Validate configuration:
terraform validate

Plan resources:
terraform plan

Apply infrastructure:
terraform apply

Destroy resources:
terraform destroy

SSH into Virtual Machine
After deployment, fetch the Public IP from Azure Portal and connect:
ssh adminuser@<PUBLIC_IP>
```
