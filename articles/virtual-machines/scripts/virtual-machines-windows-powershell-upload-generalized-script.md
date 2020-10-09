---
title: Przekaż uogólniony wirtualny dysk twardy do Azure PowerShell przykład skryptu
description: Przykładowy skrypt programu PowerShell do przekazania uogólnionego wirtualnego dysku twardego na platformę Azure i utworzenia nowej maszyny wirtualnej przy użyciu modelu wdrażania Menedżera zasobów i dysków zarządzanych.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 03e3ea745e00773272cd141aebb845465ee9890c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89072489"
---
# <a name="sample-script-to-upload-a-vhd-to-azure-and-create-a-new-vm"></a>Przykładowy skrypt do przekazania wirtualnego dysku twardego na platformę Azure i utworzenia nowej maszyny wirtualnej

Ten skrypt pobiera lokalny plik vhd z uogólnionej maszyny wirtualnej i przekazuje go na platformę Azure, tworzy obraz dysku zarządzanego i używa go do tworzenia nowej maszyny wirtualnej.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Przykładowy skrypt

```powershell
# Provide values for the variables
$resourceGroup = 'myResourceGroup'
$location = 'EastUS'
$storageaccount = 'mystorageaccount'
$storageType = 'Standard_LRS'
$containername = 'mycontainer'
$localPath = 'C:\Users\Public\Documents\Hyper-V\VHDs\generalized.vhd'
$vmName = 'myVM'
$imageName = 'myImage'
$vhdName = 'myUploadedVhd.vhd'
$diskSizeGB = '128'
$subnetName = 'mySubnet'
$vnetName = 'myVnet'
$ipName = 'myPip'
$nicName = 'myNic'
$nsgName = 'myNsg'
$ruleName = 'myRdpRule'
$computerName = 'myComputerName'
$vmSize = 'Standard_DS1_v2'

# Get the username and password to be used for the administrators account on the VM. 
# This is used when connecting to the VM using RDP.

$cred = Get-Credential

# Upload the VHD
New-AzResourceGroup -Name $resourceGroup -Location $location
New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccount -Location $location `
    -SkuName $storageType -Kind "Storage"
$urlOfUploadedImageVhd = ('https://' + $storageaccount + '.blob.core.windows.net/' + $containername + '/' + $vhdName)
Add-AzVhd -ResourceGroupName $resourceGroup -Destination $urlOfUploadedImageVhd `
    -LocalFilePath $localPath

# Note: Uploading the VHD may take awhile!

# Create a managed image from the uploaded VHD 
$imageConfig = New-AzImageConfig -Location $location
$imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized `
    -BlobUri $urlOfUploadedImageVhd
$image = New-AzImage -ImageName $imageName -ResourceGroupName $resourceGroup -Image $imageConfig
 
# Create the networking resources
$singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup -Location $location `
    -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
$pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $resourceGroup -Location $location `
    -AllocationMethod Dynamic
$rdpRule = New-AzNetworkSecurityRuleConfig -Name $ruleName -Description 'Allow RDP' -Access Allow `
    -Protocol Tcp -Direction Inbound -Priority 110 -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
$nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $resourceGroup -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
$vnet = Get-AzVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName

# Start building the VM configuration
$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize

# Set the VM image as source image for the new VM
$vm = Set-AzVMSourceImage -VM $vm -Id $image.Id

# Finish the VM configuration and add the NIC.
$vm = Set-AzVMOSDisk -VM $vm  -DiskSizeInGB $diskSizeGB -CreateOption FromImage -Caching ReadWrite
$vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

# Create the VM
New-AzVM -VM $vm -ResourceGroupName $resourceGroup -Location $location

# Verify that the VM was created
$vmList = Get-AzVM -ResourceGroupName $resourceGroup
$vmList.Name


```


<!-- 
[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-iis/create-windows-vm-iis.ps1 "Create VM IIS")] -->

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie                                                                                                             | Uwagi                                                                                                                                                                                |
|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)                           | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby.                                                                                                                          |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)                         | Tworzy konto magazynu.                                                                                                                                                           |
| [Add-AzVhd](/powershell/module/az.compute/add-azvhd)                                               | Przekazuje wirtualny dysk twardy z lokalnej maszyny wirtualnej do obiektu blob w koncie magazynu w chmurze na platformie Azure.                                                                       |
| [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig)                               | Tworzy konfigurowalny obiekt obrazu.                                                                                                                                                 |
| [Set-AzImageOsDisk](/powershell/module/az.compute/set-azimageosdisk)                               | Ustawia właściwości dysku systemu operacyjnego w obiekcie obrazu.                                                                                                                        |
| [New-AzImage](/powershell/module/az.compute/new-azimage)                                           | Tworzy nowy obraz.                                                                                                                                                                 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Tworzy konfigurację podsieci. Ta konfiguracja jest używana podczas procesu tworzenia sieci wirtualnej.                                                                                |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)                         | Tworzy sieć wirtualną.                                                                                                                                                           |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)                       | Tworzy publiczny adres IP.                                                                                                                                                         |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)                     | Tworzy interfejs sieciowy.                                                                                                                                                         |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)   | Tworzy konfiguracji reguły sieciowej grupy zabezpieczeń. Ta konfiguracja jest używana do tworzenia reguły sieciowej grupy zabezpieczeń podczas tworzenia sieciowej grupy zabezpieczeń.                                                       |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)             | Tworzy sieciową grupę zabezpieczeń.                                                                                                                                                    |
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)                         | Pobiera sieć wirtualną w grupie zasobów.                                                                                                                                          |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)                                     | Tworzy konfigurację maszyny wirtualnej. Ta konfiguracja zawiera informacje, takie jak nazwa maszyny wirtualnej, system operacyjny i poświadczenia administracyjne. Jest ona używana podczas tworzenia maszyny wirtualnej. |
| [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)                           | Określa obraz dla maszyny wirtualnej.                                                                                                                                            |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk)                                     | Ustawia właściwości dysku systemu operacyjnego dla maszyny wirtualnej.                                                                                                                      |
| [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)                   | Ustawia właściwości dysku systemu operacyjnego dla maszyny wirtualnej.                                                                                                                      |
| [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)                 | Dodaje interfejs sieciowy do maszyny wirtualnej.                                                                                                                                       |
| [New-AzVM](/powershell/module/az.compute/new-azvm)                                                 | Tworzy maszynę wirtualną.                                                                                                                                                            |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)                     | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte.                                                                                                                         |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
