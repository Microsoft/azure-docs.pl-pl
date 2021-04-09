---
title: Uaktualnianie publicznych adresów IP
titleSuffix: Azure Virtual Network
description: Uaktualnij publiczne adresy IP z warstwy Podstawowa do standardowa.
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2020
ms.author: blehr
ms.custom: references_regions
ms.openlocfilehash: 33c767d847d9e70e95b3ee1648be7852aa5cec98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100522890"
---
# <a name="upgrade-public-ip-addresses"></a>Uaktualnianie publicznych adresów IP

Publiczne adresy IP platformy Azure są tworzone przy użyciu jednostki SKU — podstawowa lub standardowa — która określa aspekty ich funkcjonalności (w tym metody alokacji, obsługę funkcji i zasoby, z którymi można kojarzyć). 

W tym artykule opisano następujące scenariusze:
* Jak uaktualnić publiczny adres IP podstawowej jednostki SKU do publicznego adresu IP jednostki SKU w warstwie Standardowa (przy użyciu programu PowerShell lub interfejsu wiersza polecenia)
* Jak przeprowadzić migrację klasycznego Zastrzeżony adres IP platformy Azure do Azure Resource Manager publicznego adresu IP podstawowej jednostki SKU

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Uaktualnij publiczny adres IP z podstawowej do standardowej jednostki SKU

Aby można było uaktualnić publiczny adres IP, nie może być skojarzony z żadnym zasobem (zobacz [Tę stronę](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) , aby uzyskać więcej informacji na temat usuwania skojarzenia publicznych adresów IP).

>[!IMPORTANT]
>Publiczne adresy IP uaktualnione z podstawowej do standardowej jednostki SKU nadal nie mają gwarantowanych [stref dostępności](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).  Przed wybraniem zasobów, z którymi należy skojarzyć adres IP, należy się upewnić, że jest to konieczne.

---
# <a name="basic-to-standard---powershell"></a>[**Basic do Standard — PowerShell**](#tab/option-upgrade-powershell)

W poniższym przykładzie przyjęto założenie, że poprzedni publiczny adres IP podstawowej jednostki SKU zostanie utworzony przy użyciu przykładu podanym na [tej stronie](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic) z podstawowym publicznym adresem IP **MyBasicPublicIP** w liście **zasobów**.

Aby uaktualnić adres IP, po prostu wykonaj poniższe polecenia przy użyciu programu PowerShell.  Należy pamiętać, że adres IP jest już przydzielony statycznie, ta sekcja może zostać pominięta.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**Basic do Standard — interfejs wiersza polecenia**](#tab/option-upgrade-cli)

W poniższym przykładzie przyjęto założenie, że poprzedni publiczny adres IP podstawowej jednostki SKU zostanie utworzony przy użyciu przykładu podanym na [tej stronie](./create-public-ip-cli.md?tabs=option-create-public-ip-basic) z podstawowym publicznym adresem IP **MyBasicPublicIP** w liście **zasobów**.

Aby uaktualnić adres IP, po prostu wykonaj poniższe polecenia przy użyciu interfejsu wiersza polecenia platformy Azure.  Należy pamiętać, że adres IP jest już przydzielony statycznie, ta sekcja może zostać pominięta.

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>Uaktualnij (Migruj) klasyczny Zastrzeżony adres IP do statycznego publicznego adresu IP

Aby skorzystać z nowych możliwości w Azure Resource Manager, można migrować istniejący publiczny statyczny adres IP — nazywane zarezerwowanymi IP — z modelu klasycznego do nowoczesnego modelu Azure Resource Manager.  Migrowany publiczny adres IP będzie podstawowym typem jednostki SKU.


---

# <a name="reserved-to-basic---powershell"></a>[**Zarezerwowane dla języka Basic — PowerShell**](#tab/option-migrate-powershell)

W poniższym przykładzie założono poprzednie utworzenie klasycznej usługi Azure Zastrzeżony adres IP **myReservedIP** w ramach **zasobu**. Innym wymaganiem wstępnym migracji jest upewnienie się, że subskrypcja Azure Resource Manager została zarejestrowana na potrzeby migracji. Opisano to szczegółowo w krokach 3 i 4 [tej strony](../virtual-machines/migration-classic-resource-manager-ps.md).

Aby przeprowadzić migrację Zastrzeżony adres IP, wykonaj poniższe polecenia przy użyciu programu PowerShell.  Uwaga: Jeśli adres IP nie jest skojarzony z żadną usługą (poniżej znajduje się usługa o nazwie Moje **usługi**), ten krok można pominąć.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
Poprzednie polecenie wyświetla wszystkie ostrzeżenia i błędy, które blokują migrację. Jeśli sprawdzanie poprawności zakończy się pomyślnie, możesz wykonać następujące kroki, aby przygotować i zatwierdzić migrację:
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
Nowa grupa zasobów w Azure Resource Manager jest tworzona przy użyciu nazwy zmigrowanego Zastrzeżony adres IP (w powyższym przykładzie będzie to grupa zasobów **myReservedIP-migrowana**).

# <a name="reserved-to-basic---cli"></a>[**Zarezerwowane dla języka Basic — interfejs wiersza polecenia**](#tab/option-migrate-cli)

W poniższym przykładzie założono poprzednie utworzenie klasycznej usługi Azure Zastrzeżony adres IP **myReservedIP** w ramach **zasobu**. Innym wymaganiem wstępnym migracji jest upewnienie się, że subskrypcja Azure Resource Manager została zarejestrowana na potrzeby migracji. Opisano to szczegółowo w krokach 3 i 4 [tej strony](../virtual-machines/migration-classic-resource-manager-cli.md).

Aby przeprowadzić migrację Zastrzeżony adres IP, wykonaj poniższe polecenia przy użyciu interfejsu wiersza polecenia platformy Azure.  Zwróć uwagę, że adres IP nie jest skojarzony z żadną usługą (poniżej znajduje się usługa  o nazwie Moja usługa **i wdrażanie i wdrożenie).** ten krok można pominąć.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
Poprzednie polecenie wyświetla wszystkie ostrzeżenia i błędy, które blokują migrację. Jeśli sprawdzanie poprawności zakończy się pomyślnie, możesz wykonać następujące kroki, aby przygotować i zatwierdzić migrację:
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
Nowa grupa zasobów w Azure Resource Manager jest tworzona przy użyciu nazwy zmigrowanego Zastrzeżony adres IP (w powyższym przykładzie będzie to grupa zasobów **myReservedIP-migrowana**).

---

## <a name="limitations"></a>Ograniczenia

* W celu uaktualnienia podstawowego publicznego adresu IP nie można go skojarzyć z żadnym zasobem platformy Azure.  Przejrzyj [Tę stronę](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) , aby uzyskać więcej informacji na temat usuwania skojarzenia publicznych adresów IP.  Podobnie w celu migrowania Zastrzeżony adres IP nie można jej skojarzyć z żadną usługą w chmurze.  Przejrzyj [Tę stronę](./remove-public-ip-address-vm.md) , aby uzyskać więcej informacji na temat usuwania skojarzenia zarezerwowanych adresów IP.  
* Publiczne adresy IP uaktualnione z podstawowej do standardowej jednostki SKU nadal nie będą miały [stref dostępności](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) i dlatego nie można ich skojarzyć z zasobem platformy Azure, który jest strefowo nadmiarowy lub zona.  Należy pamiętać, że dotyczy to tylko regionów, które oferują strefy dostępności.
* Nie można zmienić wersji z warstwy Standardowa na podstawowa.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [publicznych adresach IP](./public-ip-addresses.md#public-ip-addresses) na platformie Azure, w tym o różnicach między typami jednostek SKU, a także [ustawieniami publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Dowiedz się, jak [uaktualniać publiczne usługi równoważenia obciążenia platformy Azure z warstwy Podstawowa do standardowa](../load-balancer/upgrade-basic-standard.md).
- Zapoznaj się z [klasycznymi adresami IP zarezerwowanych platformy Azure](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) i [migracją zasobów klasycznych do Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md).
