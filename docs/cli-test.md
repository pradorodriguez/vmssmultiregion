# Day 2 and 3 Implementation - Testing the environment (OPTIONAL)

## Testing the Multiregion VMSS resources

In the following guide you will be installing the IIS service and a basic website in the VMSSs. The installation of the website will help validate the redundancy techniques that Azure provides to the applications.

## Prerequisites

### Complete the steps from the implementation guide

[Multi region VMSS implementation guide](cli-guide.md)

### Get the values of the following variables

* resourceGroupPrimary: Name of primary resource group
* resourceGroupSecondary: Name of secondary resource group
* vmssPrimary: Name of primary vmss
* vmssSecondary: Name of secondary vmss
* trafficManagerFqdn: Traffic Manager Fully Qualified Domain Name (FQDN)

## Step by Step guide to test the configuration

### Open the Cloud Shell icon

* In the Azure Portal, click the top-right icon named "Cloud Shell"
  * Inside the Cloud Shell, in the top-left icon select "PowerShell"
or
* Open a new browser tab and go to the following website:

<https://shell.azure.com>

* Paste the following commands in the Azure Cloud Shell
  * Inside the Cloud Shell, in the top-left icon select "PowerShell"

_[Copy & Paste instructions for Azure Cloud Shell](https://learn.microsoft.com/en-us/azure/cloud-shell/using-the-shell-window#copy-and-paste)_

### Paste the following variables

```text
$resourceGroupPrimary="<replace-with-name-of-primary-resource-group>"
$resourceGroupSecondary="<replace-with-name-of-secondary-resource-group>"
$vmssPrimary="<replace-with-name-of-primary-vmss>"
$vmssSecondary="<replace-with-name-of-secondary-vmss>"
```

### Reference the Powershell script to install IIS and a basic website

```text
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```

### Get the resource information of the primary and secondary VMSS

```text
$vmssPrimaryExtension = Get-AzVmss -ResourceGroupName $resourceGroupPrimary -VMScaleSetName $vmssPrimary
$vmssSecondaryExtension = Get-AzVmss -ResourceGroupName $resourceGroupSecondary -VMScaleSetName $vmssSecondary
```

### Add the Custom Script Extension to the primary and secondary VMSS

#### Primary Scale Set

```text
$vmssPrimaryExtension = Add-AzVmssExtension `
  -VirtualMachineScaleSet $vmssPrimaryExtension `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig
```

#### Secondary Scale Set

```text
$vmssSecondaryExtension = Add-AzVmssExtension `
  -VirtualMachineScaleSet $vmssSecondaryExtension `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig
```

### Apply the Custom Script Extension to the VMSSs

#### Apply the Custom Script Extension to the primary VMSS

```text
Update-AzVmss -ResourceGroupName $resourceGroupPrimary -Name $vmssPrimary -VirtualMachineScaleSet $vmssPrimaryExtension
```

#### Apply the Custom Script Extension to the secondary VMSS

```text
Update-AzVmss -ResourceGroupName $resourceGroupSecondary -Name $vmssSecondary -VirtualMachineScaleSet $vmssSecondaryExtension
```

### Test the Multi-region availability

* Open a browser and paste the Traffic Manager FQDN

```text
http://<replace-with-trafficManagerFqdn>
```

* The website should be up and running. A reference to the Primary VMSS name should be displayed.

* Stop the primary VMSS.

* Wait 90 seconds for the Traffic Manager to route traffic to Secondary VMSS.

* Refresh the Traffic Manager FQDN in the portal.

```text
http://<replace-with-trafficManagerFqdn>
```

* The website should be up and running. A reference to the Secondary VMSS name should be displayed.
