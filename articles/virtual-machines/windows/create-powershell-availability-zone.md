---
title: Tworzenie maszyny wirtualnej z strefą przy użyciu Azure PowerShell
description: Tworzenie maszyny wirtualnej w strefie dostępności przy użyciu Azure PowerShell
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 7167c5806fde883a84878d4947dba9fd8c095f69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558835"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-azure-powershell"></a>Tworzenie maszyny wirtualnej w strefie dostępności przy użyciu Azure PowerShell

W tym artykule przedstawiono szczegółowe informacje dotyczące tworzenia maszyny wirtualnej z systemem Windows Server 2016 w strefie dostępności platformy Azure przy użyciu programu Azure PowerShell. [Strefa dostępności](../../availability-zones/az-overview.md) to fizycznie oddzielona strefa w regionie świadczenia usługi Azure. Strefy dostępności chronią aplikacje i dane, zmniejszając prawdopodobieństwo wystąpienia awarii lub utraty całego centrum danych.

Aby użyć strefy dostępność, utwórz maszynę wirtualną w [obsługiwanym regionie platformy Azure](../../availability-zones/az-region.md).

 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

## <a name="check-vm-sku-availability"></a>Sprawdzanie dostępności jednostki SKU maszyny wirtualnej
Dostępność rozmiarów maszyn wirtualnych lub jednostek SKU może różnić się w zależności od regionu i strefy. Aby uzyskać pomoc w planowaniu użycia stref dostępności, możesz wyświetlić listę dostępnych jednostek SKU maszyn wirtualnych według regionu i strefy świadczenia usługi Azure. Ta możliwość gwarantuje wybranie odpowiedniego rozmiaru maszyny wirtualnej oraz uzyskanie żądanej odporności w różnych strefach. Aby uzyskać więcej informacji o różnych typach i rozmiarach maszyn wirtualnych, zobacz [Omówienie rozmiarów maszyn wirtualnych](../sizes.md).

Możesz wyświetlić dostępne jednostki SKU maszyny wirtualnej przy użyciu polecenia [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku). Następujący przykład zawiera listę dostępnych jednostek SKU maszyn wirtualnych w regionie *eastus2*:

```powershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

Rezultat jest podobny do poniższego, skróconego przykładu, który pokazuje strefy dostępności, w których dostępne są poszczególne rozmiary maszyn wirtualnych:

```powershell
ResourceType                Name  Location      Zones   [...]
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. W tym przykładzie utworzymy grupę zasobów o nazwie *myResourceGroup* w regionie *eastus2*. 

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS2
```

## <a name="create-networking-resources"></a>Tworzenie zasobów sieciowych

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Tworzenie sieci wirtualnej, podsieci i publicznego adresu IP 
Te zasoby są używane do zapewniania łączności sieciowej z maszyną wirtualną i nawiązywania połączenia z Internetem. Utwórz adres IP w strefie dostępności, w tym przykładzie *2*. W ostatnim kroku utworzono maszynę wirtualną w tej samej strefie, która została użyta do utworzenia adresu IP.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myVNet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)" -Sku Standard
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Tworzenie sieciowej grupy zabezpieczeń i reguły sieciowej grupy zabezpieczeń 
Sieciowa grupa zabezpieczeń chroni maszynę wirtualną za pomocą reguł ruchu przychodzącego i wychodzącego. W tym przypadku jest tworzona reguła ruchu przychodzącego dla portu 3389, która umożliwia nawiązywanie przychodzących połączeń pulpitu zdalnego. Chcemy też utworzyć regułę ruchu przychodzącego dla portu 80, która zezwala na ruch przychodzący w sieci Web.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Tworzenie karty sieciowej dla maszyny wirtualnej 
Utwórz kartę sieciową maszyny wirtualnej za pomocą polecenia [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Karta sieciowa łączy maszynę wirtualną z podsiecią, sieciową grupą zabezpieczeń i publicznym adresem IP.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz konfigurację maszyny wirtualnej. Ta konfiguracja zawiera ustawienia, które są używane podczas wdrażania maszyny wirtualnej, takie jak obraz maszyny wirtualnej, rozmiar i konfiguracja uwierzytelniania. Rozmiar *Standardowa_DS1_v2* używany w tym przykładzie jest obsługiwany w strefach dostępności. Konfiguracja ta określa również strefę dostępności ustawioną podczas tworzenia adresu IP. Podczas wykonywania tego kroku jest wyświetlany monit o poświadczenia. Wprowadzane wartości są konfigurowane jako nazwa użytkownika i hasło dla maszyny wirtualnej.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
```

Utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm).

```powershell
New-AzVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="confirm-zone-for-managed-disk"></a>Potwierdzanie strefy dla dysku zarządzanego

Zasób adresu IP maszyny wirtualnej został utworzony w tej samej strefie dostępności co maszyna wirtualna. Zasób dysku zarządzanego maszyny wirtualnej jest tworzony w tej samej strefie dostępności. Można to sprawdzić za pomocą polecenia [Get-AzDisk](/powershell/module/az.compute/get-azdisk):

```powershell
Get-AzDisk -ResourceGroupName myResourceGroup
```

Dane wyjściowe pokazują, że dysk zarządzany znajduje się w tej samej strefie dostępności co maszyna wirtualna:

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx//resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia maszyny wirtualnej w strefie dostępności. Dowiedz się więcej o [dostępności](../availability.md) dla maszyn wirtualnych platformy Azure.