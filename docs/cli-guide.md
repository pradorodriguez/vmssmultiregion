# Day 2 and 3 Implementation

## Deployment guide of an Azure Virtual Machine Scale Set in a multi-region environment

Add Description

* Topology (add hyperlink)

## Prerequisites

* An Azure account with sufficient permissions to create resources

<https://learn.microsoft.com/en-us/dotnet/azure/create-azure-account>

## List of Azure Resources

The following resources will be deployed as part of this guide. All the steps to create the resources will be specified, but you may reuse existing services.

* Resource Group
* VNET
* Virtual Network (VNET)
* Network Security Group (NSG)
* Load Balancer
* Traffic Manager
* Virtual Machine Scale Set
* Azure Monitor
* Log Analytics

## Step by Step guide to deploy a multi-region VMSS

### Log and authenticate to the Azure Portal

<https://portal.azure.com/>

### Open the Cloud Shell icon

* In the Azure Portal, click the top-right icon named "Cloud Shell" 
or
* Open a new browser tab and go to the following website:

<https://shell.azure.com>

* Paste the following commands in the Azure Cloud Shell

_[Copy & Paste instructions for Azure Cloud Shell](https://learn.microsoft.com/en-us/azure/cloud-shell/using-the-shell-window#copy-and-paste)_

### Define the following variables

```text
let "randomString=$RANDOM*$RANDOM"
regionPrimary="eastus"
regionSecondary="westus"
resourceGroupPrimary="rgpri$randomString"
resourceGroupSecondary="rgsec$randomString"
vnetPrimary="vnetpri$randomString"
vnetSecondary="vnetsec$randomString"
subnetCIDRPrimary="10.0.0.0/16"
subnetCIDRSecondary="10.1.0.0/16"
subnetPrefixPrimary="10.0.0.0/24"
subnetPrefixSecondary="10.1.0.0/24"
nsgPrimary="nsgpri$randomString"
nsgSecondary="nsgsec$randomString"
loadBalancerPrimary="lbpri$randomString"
loadBalancerSecondary="lbsec$randomString"
trafficManager="tf$randomString"
vmssPrimary="vmsspri$randomString"
vmssSecondary="vmsssec$randomString"
logAnalyticsWorkspace="la$randomString"
adminUsername="adminuser"
adminPassword="pwd$randomString"
echo randomString: $randomString
```

### Create Primary and Secondary Resource Group

```text
az group create --name $resourceGroupPrimary --location $regionPrimary
az group create --name $resourceGroupSecondary --location $regionSecondary
```

### Create Primary and Secondary VNET

```text
az network vnet create --name $vnetPrimary --resource-group $resourceGroupPrimary --address-prefix $subnetPrimary --subnet-name subnet-1 --subnet-prefixes $subnetPrefixPrimary
az network vnet create --name $vnetSecondary --resource-group $resourceGroupSecondary --address-prefix $subnetSecondary --subnet-name subnet-1 --subnet-prefixes $subnetPrefixSecondary
```
