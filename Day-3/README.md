# Azure Resources and Resource Groups

## What is an Azure Resource?

An **Azure resource** is anything that you create and use in Azure. If you create something in Azure, it is called a resource.

Examples:

* Virtual Machine (VM)
* Storage Account
* Database
* Virtual Network

---

## What is a Resource Group?

A **Resource Group** is like a **folder** that holds related Azure resources together.

Simple explanation:

* One application = one resource group
* All related resources are kept in one place

Example:
If you create:

* 1 VM
* 1 Storage Account
* 1 Virtual Network

All of them can be placed inside **one resource group**.

Deleting a resource group will delete **all resources inside it**.

---

## Why Do We Need Resource Groups?

Resource groups help to:

* Organize resources easily
* Manage permissions
* Track cost and usage
* Delete everything at once when no longer needed

---

## Azure Resource Manager (ARM)

**Azure Resource Manager (ARM)** is the service that Azure uses to manage resources.
 
* ARM helps create, update, and delete resources
* ARM ensures resources are managed in a proper and consistent way
* ARM works behind the scenes whenever we use the Azure Portal or CLI

Every action in Azure goes through ARM.

---

## How Can We Manage Resources?

Resources and resource groups can be managed using:

* Azure Portal (UI)
* Azure CLI (command line)
* ARM templates (for automation)

---

## Important Points to Remember

* Every Azure resource must belong to **one resource group only**
* A resource group can contain **many resources**
* Resource groups are created inside an **Azure subscription**
* ARM is responsible for managing all Azure resources

---

## Conclusion

Resources are the building blocks in Azure, and resource groups help organize them. Azure Resource Manager makes sure all resources are created and managed properly.
