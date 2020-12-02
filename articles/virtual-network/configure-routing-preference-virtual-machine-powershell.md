---
title: Konfigurowanie preferencji routingu dla maszyny wirtualnej Azure PowerShell
description: Dowiedz się, jak utworzyć maszynę wirtualną z publicznym adresem IP z wyborem preferencji routingu za pomocą Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: mnayak
ms.openlocfilehash: 65f5b27ecc7b526c4b3e4f5ed62ac90149df1ce1
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510611"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-powershell"></a>Konfigurowanie preferencji routingu dla maszyny wirtualnej przy użyciu Azure PowerShell

W tym artykule opisano sposób konfigurowania preferencji routingu dla maszyny wirtualnej. Ruch związany z Internetem z maszyny wirtualnej będzie kierowany przez sieć usługodawcy internetowego w przypadku wybrania opcji **Internet** jako preferencji routingu. Domyślny Routing odbywa się za pośrednictwem sieci globalnej firmy Microsoft.

W tym artykule pokazano, jak utworzyć maszynę wirtualną z publicznym adresem IP, która jest ustawiona na kierowanie ruchu przez sieć usługodawcy internetowego przy użyciu Azure PowerShell.

> [!IMPORTANT]
> Preferencje routingu są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
1. Jeśli używasz Cloud Shell, przejdź do kroku 2. Otwórz sesję polecenia i zaloguj się do platformy Azure za pomocą usługi `Connect-AzAccount` .
2. Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów w regionie platformy Azure Wschodnie stany USA:

    ```azurepowershell
    $rg = New-AzResourceGroup -Name MyResourceGroup -Location EastUS
    ```

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać dostęp do maszyn wirtualnych z Internetu, potrzebne są publiczne adresy IP. Utwórz publiczne adresy IP przy użyciu programu [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). W poniższym przykładzie jest tworzony publiczny adres IP IPv4 o nazwie *MyPublicIP* Routing preferencji Type *Internet* w *grupie zasobów zasobu w regionie* *Wschodnie stany USA* :

```azurepowershell-interactive
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Przed wdrożeniem maszyny wirtualnej należy utworzyć pomocnicze zasoby sieciowe — sieciową grupę zabezpieczeń, sieć wirtualną i wirtualną kartę sieciową.

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń przy użyciu polecenia [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). Poniższy przykład tworzy sieciowej grupy zabezpieczeń o nazwie *myNSG*

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myNSG"
```

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną przy użyciu polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myVNET* z *podsiecią*:

### <a name="create-a-subnet"></a>Tworzenie podsieci

```azurepowershell
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "mySubnet" `
-AddressPrefix "10.0.0.0/24"
```

```azurepowershell
# Create a virtual network
$vnet = New-AzVirtualNetwork `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myVNET" `
-AddressPrefix "10.0.0.0/16" `
-Subnet $subnet
```

### <a name="create-a-nic"></a>Tworzenie karty sieciowej

Tworzenie wirtualnych kart sieciowych za pomocą programu [New-AzNetworkInterface] (/PowerShell/module/AZ.Network/New-aznetworkinterface. Poniższy przykład tworzy wirtualną kartę sieciową.

```azurepowershell
# Create an IP Config
$ipconfig=New-AzNetworkInterfaceIpConfig `
-Name myIpConfig `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-PublicIpAddress  $publicIp

# Create a NIC
$nic = New-AzNetworkInterface `
-Name "mynic" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $ipconfig 
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Ustaw nazwę użytkownika i hasło administratora maszyn wirtualnych przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
 $cred = get-credential -Message "Routing Preference SAMPLE:  Please enter the Administrator credential to log into the VM."
```

Następnie utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm). Poniższy przykład obejmuje tworzenie dwóch maszyn wirtualnych oraz wymaganych składników sieci wirtualnej, jeśli jeszcze nie istnieją.

```azurepowershell
 $vmsize = "Standard_A2"
 $ImagePublisher = "MicrosoftWindowsServer"
 $imageOffer = "WindowsServer"
 $imageSKU = "2019-Datacenter"

 $vmName= "myVM"
 $vmconfig = New-AzVMConfig -VMName $vmName -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption "FromImage" | Add-AzVMNetworkInterface -Id $nic.Id 
 $VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $vmconfig
```

## <a name="allow-network-traffic-to-the-vm"></a>Zezwalaj na ruch sieciowy do maszyny wirtualnej

Aby można było połączyć się z publicznym adresem IP z Internetu, należy się upewnić, że wymagane porty są otwarte w dowolnej sieciowej grupie zabezpieczeń, która może być skojarzona z interfejsem sieciowym, podsiecią, w której znajduje się interfejs sieciowy, lub obie. Efektywne reguły zabezpieczeń dla interfejsu sieciowego i jego podsieci można wyświetlić przy użyciu [portalu](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [interfejsu wiersza polecenia](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)lub [programu PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

 ```azurepowershell
 Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [preferencjach routingu w publicznych adresach IP](routing-preference-overview.md).
* Dowiedz się więcej o [publicznych adresach IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) na platformie Azure.
* Dowiedz się więcej o [ustawieniach publicznych adresów IP](virtual-network-public-ip-address.md#create-a-public-ip-address).
