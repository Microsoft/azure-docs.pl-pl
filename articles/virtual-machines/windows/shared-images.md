---
title: Tworzenie udostępnionych obrazów maszyn wirtualnych za pomocą programu Azure PowerShell
description: Dowiedz się, jak utworzyć obraz udostępnionej maszyny wirtualnej na platformie Azure za pomocą programu Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: cfaf545ad2388688666a04076559ff82b7a5d120
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458066"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Tworzenie udostępnionej galerii obrazów za pomocą programu Azure PowerShell 

[Udostępniona galeria obrazów](shared-image-galleries.md) upraszcza niestandardowe udostępnianie obrazów w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą być używane do uruchamiania zadań wdrażania, takich jak wstępne ładowanie aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom w organizacji, w obrębie lub między regionami, w dzierżawie usługi AAD. Wybierz obrazy, które chcesz udostępnić, regiony, w których chcesz je udostępnić i komu chcesz je udostępnić. Można utworzyć wiele galerii, aby logicznie grupować udostępnione obrazy. 

Galeria jest zasobem najwyższego poziomu, który zapewnia pełną kontrolę dostępu opartą na rolach (RBAC). Obrazy mogą być wersjona i można wybrać do replikacji każdej wersji obrazu do innego zestawu regionów platformy Azure. Galeria działa tylko z zarządzanymi obrazami.

Funkcja Galeria obrazów udostępnionych ma wiele typów zasobów. Będziemy używać lub budować te w tym artykule:

| Zasób | Opis|
|----------|------------|
| **Obraz zarządzany** | Jest to podstawowy obraz, który może być używany samodzielnie lub używany do tworzenia **wersji obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na uogólnionych maszynach wirtualnych. Obraz zarządzany jest specjalnym typem dysku VHD, który może służyć do tworzenia wielu maszyn wirtualnych i może być teraz używany do tworzenia wersji obrazów udostępnionych. |
| **Galeria zdjęć** | Podobnie jak w portalu Azure **Marketplace, galeria obrazów** jest repozytorium do zarządzania i udostępniania obrazów, ale można kontrolować, kto ma dostęp. |
| **Definicja obrazu** | Obrazy są definiowane w galerii i zawierają informacje o obrazie i wymaganiach dotyczących używania go wewnętrznie. Obejmuje to, czy obraz jest Windows lub Linux, informacje o wersji i minimalne i maksymalne wymagania dotyczące pamięci. Jest to definicja typu obrazu. |
| **Wersja obrazu** | **Wersja obrazu** jest to, czego używasz do tworzenia maszyny Wirtualnej podczas korzystania z galerii. W zależności od środowiska może być dostępnych wiele wersji obrazu. Podobnie jak w przypadku obrazu zarządzanego, podczas tworzenia maszyny Wirtualnej przy użyciu **wersji obrazu** wersja obrazu jest używana do tworzenia nowych dysków dla maszyny Wirtualnej. Wersje obrazów mogą być używane wiele razy. |

Dla każdego 20 maszyn wirtualnych, które tworzysz jednocześnie, zaleca się zachować jedną replikę. Na przykład jeśli tworzysz 120 maszyn wirtualnych jednocześnie przy użyciu tego samego obrazu w regionie, zalecamy zachować co najmniej 6 replik obrazu. Aby uzyskać więcej informacji, zobacz [Skalowanie](/azure/virtual-machines/windows/shared-image-galleries#scaling).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć przykład w tym artykule, musisz mieć istniejący obraz zarządzany. Możesz wykonać [samouczek: Utwórz niestandardowy obraz maszyny Wirtualnej platformy Azure za pomocą programu Azure PowerShell,](tutorial-custom-images.md) aby utworzyć go w razie potrzeby. Jeśli obraz zarządzany zawiera dysk danych, rozmiar dysku danych nie może być większy niż 1 TB.

Podczas pracy nad tym artykułem w razie potrzeby zastąp nazwy grupy zasobów i maszyn wirtualnych.

[!INCLUDE [virtual-machines-common-shared-images-powershell](../../../includes/virtual-machines-common-shared-images-powershell.md)]

 
## <a name="create-vms-from-an-image"></a>Tworzenie maszyn wirtualnych na podstawie obrazu

Po zakończeniu wersji obrazu można utworzyć jedną lub więcej nowych maszyn wirtualnych. Korzystanie z polecenia cmdlet [New-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 

W tym przykładzie utworzy się maszynę wirtualną o nazwie *myVMfromImage*, w *myResourceGroup* w centrum danych *południowo-środkowej usa.*


```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>Następne kroki
[Usługa Azure Image Builder (wersja zapoznawcza)](image-builder-overview.md) może pomóc w automatyzacji tworzenia wersji obrazu, można nawet użyć go do aktualizacji i [utworzenia nowej wersji obrazu z istniejącej wersji obrazu](image-builder-gallery-update-image-version.md). 

Zasób Shared Image Gallery można również utworzyć przy użyciu szablonów. Dostępnych jest kilka szablonów szybki start platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny Wirtualnej z wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Aby uzyskać więcej informacji na temat galerii obrazów udostępnionych, zobacz [Omówienie](shared-image-galleries.md). Jeśli napotkasz problemy, zobacz [Rozwiązywanie problemów z udostępnionymi galeriami obrazów](troubleshooting-shared-images.md).

