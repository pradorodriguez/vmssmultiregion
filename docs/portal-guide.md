# Day 2 and 3 Implementation - Azure Portal Guide

## Deployment guide of an Azure Virtual Machine Scale Set in a multi-region environment

The purpose of this guide is to implement a resilience and high available environment for your applications using Virtual Machine Scale Sets (VMSS). In this guide the VMSS will be deployed in a multi-zone and multi-region environment. With this configuration, your applications will be tolerant to single VM, datacenter and region failures, with an automatic failover due to the use of Regional Load Balancer solutions (Azure Traffic Manager).

### Reference architecture

![Architecture](../images/vmss-high-availability-multi-region-v1.jpeg)

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

## Step by Step guide to deploy a multi-region VMSS

### Log and authenticate to the Azure Portal

<https://portal.azure.com/>

### Create Primary and Secondary Resource Group

<https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups>

### Create Primary and Secondary VNET

<https://learn.microsoft.com/en-us/azure/virtual-network/quick-create-portal#create-a-virtual-network-and-bastion-host>

### Peer Primary and Secondary virtual networks (VNET Peering)

<https://learn.microsoft.com/en-us/azure/virtual-network/virtual-network-manage-peering?tabs=peering-portal#create-a-peering>

### Create the Primary and Secondary NSGs

<https://learn.microsoft.com/en-us/azure/virtual-network/manage-network-security-group?tabs=network-security-group-portal#create-a-network-security-group>

### Associate NSGs to the Primary and Secondary subnets

<https://learn.microsoft.com/en-us/azure/virtual-network/manage-network-security-group?tabs=network-security-group-portal#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet>

### Create a NSG security rule to allow inbound HTTP traffic

<https://learn.microsoft.com/en-us/azure/virtual-network/manage-network-security-group?tabs=network-security-group-portal#create-a-security-rule>

### Create the Primary and Secondary Virtual Machine Scale Sets (VMSS) with Load Balancers

<https://learn.microsoft.com/en-us/azure/virtual-machine-scale-sets/quick-create-portal#create-a-load-balancer>

_Note: when creating the VMSS, select Windows Server 2022 Datacenter as the image_

* Image: Windows Server 2022 Datacenter: Azure Edition - x64 Gen2

### Add an HTTP health probe for the the Primary and Secondary Load Balancers

<https://learn.microsoft.com/en-us/azure/load-balancer/manage-probes-how-to#add-an-http-health-probe>

### Add a load-balancing rule for the the Primary and Secondary Load Balancers

<https://learn.microsoft.com/en-us/azure/load-balancer/manage-rules-how-to#add-a-load-balancing-rule>

### Add a DNS Name Label for the Primary and Secondary Public IPs

_Note: The Primary and Secondary Public IPs are assigned to the Primary and Secondary Load Balancers respectively._

<https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address>

### Create a Traffic Manager profile

<https://learn.microsoft.com/en-us/azure/traffic-manager/quickstart-create-traffic-manager-profile#create-a-traffic-manager-profile>

### Add the Primary and Secondary Load Balancers as Traffic Manager endpoints

<https://learn.microsoft.com/en-us/azure/traffic-manager/quickstart-create-traffic-manager-profile#add-traffic-manager-endpoints>

_Note: to add Load Balancers to a Traffic Manager profile, the target resource type must be Public IP Address:_

* target resource type: Public IP Address
* Public IP Address: Select the Public IP Address of the Load Balancer
