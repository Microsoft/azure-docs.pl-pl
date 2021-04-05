---
title: Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP — PowerShell | Microsoft Docs
description: Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP przy użyciu programu PowerShell. Statyczne publiczne adresy IP to adresy, które nigdy nie zmieniają się.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 047a6db82e65c97deba5270d181f72315a67e82c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791260"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP przy użyciu programu PowerShell


Można utworzyć maszynę wirtualną ze statycznym publicznym adresem IP. Publiczny adres IP umożliwia komunikację z maszyną wirtualną z Internetu. Przypisz statyczny publiczny adres IP, a nie adres dynamiczny, aby upewnić się, że adres nigdy nie ulegnie zmianie. Dowiedz się więcej na temat [statycznych publicznych adresów IP](./public-ip-addresses.md#allocation-method). Aby zmienić publiczny adres IP przypisany do istniejącej maszyny wirtualnej z dynamicznego na statyczny lub do pracy z prywatnymi adresami IP, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md). Publiczne adresy IP mają [opłata nominalną](https://azure.microsoft.com/pricing/details/ip-addresses), a liczba publicznych adresów IP, których można użyć na subskrypcję, jest [ograniczona](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Poniższe kroki można wykonać z komputera lokalnego lub przy użyciu Azure Cloud Shell. Aby korzystać z komputera lokalnego, upewnij się, że [zainstalowano Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby użyć Azure Cloud Shell, wybierz opcję **Wypróbuj** w prawym górnym rogu dowolnego poniższego pola polecenia. Cloud Shell umożliwia zalogowanie się do platformy Azure.

1. Jeśli używasz Cloud Shell, przejdź do kroku 2. Otwórz sesję polecenia i zaloguj się do platformy Azure za pomocą usługi `Connect-AzAccount` .
2. Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów w regionie platformy Azure Wschodnie stany USA:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](/powershell/module/az.Compute/New-azVM) . `-AllocationMethod "Static"`Opcja przypisuje statyczny publiczny adres IP do maszyny wirtualnej. Poniższy przykład tworzy maszynę wirtualną z systemem Windows Server przy użyciu statycznego publicznego adresu IP podstawowej jednostki SKU o nazwie *myPublicIpAddress*. Po wyświetleniu monitu podaj nazwę użytkownika i hasło, które będą używane jako poświadczenia logowania dla maszyny wirtualnej:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Jeśli publiczny adres IP musi być jednostką SKU w warstwie Standardowa, musisz [utworzyć publiczny adres IP](virtual-network-public-ip-address.md#create-a-public-ip-address), [utworzyć interfejs sieciowy](virtual-network-network-interface.md#create-a-network-interface), [przypisać publiczny adres IP do interfejsu sieciowego](virtual-network-network-interface-addresses.md#add-ip-addresses), a następnie [utworzyć maszynę wirtualną przy użyciu interfejsu sieciowego](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)w oddzielnych krokach. Dowiedz się więcej o jednostkach [SKU publicznych adresów IP](./public-ip-addresses.md#sku). Jeśli maszyna wirtualna zostanie dodana do puli zaplecza Azure Load Balancer publicznej, jednostka SKU publicznego adresu IP maszyny wirtualnej musi być zgodna z jednostką SKU publicznego adresu IP modułu równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Azure Load Balancer](../load-balancer/skus.md).

4. Wyświetl przypisany publiczny adres IP i upewnij się, że został on utworzony jako adres statyczny, przy użyciu elementu [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Na platformie Azure przypisano publiczny adres IP z adresów używanych w regionie, w którym została utworzona maszyna wirtualna. Możesz pobrać listę zakresów (prefiksów) dla następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Instytucje rządowe USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Nie należy modyfikować ustawień adresu IP w ramach systemu operacyjnego maszyny wirtualnej. System operacyjny nie rozpoznaje publicznych adresów IP platformy Azure. Mimo że można dodać ustawienia prywatnych adresów IP do systemu operacyjnego, zaleca się, aby nie było to możliwe, chyba że jest to konieczne, a nie do momentu [dodania prywatnego adresu IP do systemu operacyjnego](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [publicznych adresach IP](./public-ip-addresses.md#public-ip-addresses) na platformie Azure
- Dowiedz się więcej na temat wszystkich [ustawień publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Dowiedz się więcej o prywatnych adresach [IP](./private-ip-addresses.md) i przypisywaniu [statycznego prywatnego adresu IP](virtual-network-network-interface-addresses.md#add-ip-addresses) do maszyny wirtualnej platformy Azure
- Dowiedz się więcej o tworzeniu maszyn wirtualnych z systemem [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
