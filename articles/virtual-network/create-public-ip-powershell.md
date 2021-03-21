---
title: Tworzenie publicznego adresu IP — Azure PowerShell
description: Dowiedz się, jak utworzyć publiczny adres IP przy użyciu Azure PowerShell
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: f81e76e32bc0a50b945c54cdfcac0575540484bc
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502437"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>Szybki Start: Tworzenie publicznego adresu IP przy użyciu Azure PowerShell

W tym artykule pokazano, jak utworzyć zasób publicznego adresu IP przy użyciu Azure PowerShell. Aby uzyskać więcej informacji na temat zasobów, z którymi może być skojarzone, różnica między podstawową i standardową jednostką SKU i innymi powiązanymi informacjami, zobacz [publiczne adresy IP](./public-ip-addresses.md).  Na potrzeby tego przykładu będziemy skupić się tylko na adresach IPv4. Aby uzyskać więcej informacji na temat adresów IPv6, zobacz [IPv6 dla sieci wirtualnej platformy Azure](./ipv6-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Azure PowerShell zainstalowane lokalnie lub Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów przy użyciu funkcji [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) o nazwie Moja **resourceName** w lokalizacji **eastus2** .

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'

New-AzResourceGroup -Name $rg -Location $loc
```
## <a name="create-public-ip"></a>Utwórz publiczny adres IP

---
# <a name="standard-sku---using-zones"></a>[**Standardowa jednostka SKU — używanie stref**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Następujące polecenie działa dla AZ. Network module w wersji 4.5.0 lub nowszej.  Aby uzyskać więcej informacji na temat aktualnie używanych modułów programu PowerShell, zapoznaj się z [dokumentacją PowerShellGet](/powershell/module/powershellget/).

Użyj [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) , aby utworzyć strefę w warstwie Standardowa — nadmiarowy publiczny adres IP o nazwie **MyStandardZRPublicIP** w liście **zasobów**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZRPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 1,2,3

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```
> [!IMPORTANT]
> Dla modułów AZ. Network, które są starsze niż 4.5.0, uruchom powyższe polecenie bez określania parametru strefy, aby utworzyć nadmiarowy adres IP strefy. 
>

Aby utworzyć standardowy publiczny adres IP stref w Strefa 2 o nazwie **myStandardZonalPublicIP** w liście **zasobów**, użyj następującego polecenia:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZonalPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 2

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```

Należy pamiętać, że powyższe opcje dla stref są tylko prawidłowymi opcjami w regionach, w których [strefy dostępności](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**Standardowa jednostka SKU — brak stref**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Następujące polecenie działa dla AZ. Network module w wersji 4.5.0 lub nowszej.  Aby uzyskać więcej informacji na temat aktualnie używanych modułów programu PowerShell, zapoznaj się z [dokumentacją PowerShellGet](/powershell/module/powershellget/).

Użyj [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) , aby utworzyć standardowy publiczny adres IP jako zasób niebędący strefą o nazwie **MyStandardPublicIP** w liście **zasobów**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardPublicIP'
$sku = 'Standard'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```

Ten wybór jest prawidłowy we wszystkich regionach i jest domyślnym wyborem dla standardowych publicznych adresów IP w regionach bez [strefy dostępności](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="basic-sku"></a>[**Podstawowy SKU**](#tab/option-create-public-ip-basic)

Użyj [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) , aby utworzyć podstawowy statyczny publiczny adres IP o nazwie **MyBasicPublicIP** w liście **zasobów**.  Podstawowe publiczne adresy IP nie mają koncepcji dotyczących stref dostępności.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myBasicPublicIP'
$sku = 'Basic'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```
Jeśli jest akceptowalny do zmiany adresu IP z upływem czasu, można wybrać opcję **dynamicznego** przypisywania adresów IP, zmieniając metodę AllocationMethod jako na "dynamiczny".

---

## <a name="additional-information"></a>Dodatkowe informacje 

Aby uzyskać więcej szczegółowych informacji na temat poszczególnych zmiennych wymienionych powyżej, zobacz [Zarządzanie publicznymi adresami IP](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Następne kroki
- Skojarz [publiczny adres IP z maszyną wirtualną](./associate-public-ip-address-vm.md#azure-portal)
- Dowiedz się więcej o [publicznych adresach IP](./public-ip-addresses.md#public-ip-addresses) na platformie Azure.
- Dowiedz się więcej na temat wszystkich [ustawień publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address).