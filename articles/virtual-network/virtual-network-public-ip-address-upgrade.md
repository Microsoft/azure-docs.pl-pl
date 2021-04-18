---
title: Uaktualnianie publicznych adresów IP
titleSuffix: Azure Virtual Network
description: Uaktualnij publiczne adresy IP z podstawowego do standardowego.
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
ms.openlocfilehash: 74df338fd888bd7f654ddfc2fc5f9dddf10e84ab
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598418"
---
# <a name="upgrade-public-ip-addresses"></a>Uaktualnianie publicznych adresów IP

Publiczne adresy IP platformy Azure są tworzone przy użyciu podstawowej lub standardowej wersji SKU, która określa aspekty ich funkcjonalności (w tym metodę alokacji, obsługę funkcji i zasoby, z którymi można je skojarzyć). 

W tym artykule o przejrzenie następujących scenariuszy:
* Jak uaktualnić publiczny adres IP podstawowej wersji SKU do publicznego adresu IP standardowej wersji SKU (przy użyciu programu PowerShell lub interfejsu wiersza polecenia)
* Jak przeprowadzić migrację klasycznej usługi Azure Zastrzeżony adres IP do publicznego adresu IP Azure Resource Manager podstawowej wersji SKU

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Uaktualnianie publicznego adresu IP z podstawowej do standardowej wersji SKU

Aby uaktualnić publiczny adres IP, nie może być [](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) skojarzony z żadnym zasobem (zobacz tę stronę, aby uzyskać więcej informacji na temat sposobu skojarzenia publicznych adresów IP).

>[!IMPORTANT]
>Publiczne ip uaktualnione z podstawowej do standardowej wersji SKU nadal nie mają gwarantowanych [stref dostępności.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)  Upewnij się, że o tym pamiętać podczas wybierania zasobów, z którymi chcesz skojarzyć adres IP.

---
# <a name="basic-to-standard---powershell"></a>[**Podstawowa–Standardowa — PowerShell**](#tab/option-upgrade-powershell)

W poniższym przykładzie założono poprzednie utworzenie publicznego adresu IP [](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic) podstawowej wersji SKU, korzystając z przykładu podanego na tej stronie z publicznym podstawowym adresem IP **myBasicPublicIP** w grupie **myResourceGroup.**

Aby uaktualnić adres IP, po prostu wykonaj poniższe polecenia przy użyciu programu PowerShell.  Zwróć uwagę, że jeśli adres IP jest już przydzielony statycznie, można pominąć sekcję.

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

# <a name="basic-to-standard---cli"></a>[**Podstawowa–Standardowa — interfejs wiersza polecenia**](#tab/option-upgrade-cli)

W poniższym przykładzie założono poprzednie utworzenie publicznego adresu IP [](./create-public-ip-cli.md?tabs=option-create-public-ip-basic) podstawowej wersji SKU, korzystając z przykładu podanego na tej stronie z publicznym podstawowym adresem IP **myBasicPublicIP** w **grupie myResourceGroup.**

Aby uaktualnić adres IP, po prostu wykonaj poniższe polecenia przy użyciu interfejsu wiersza polecenia platformy Azure.  Zwróć uwagę, że jeśli adres IP jest już przydzielony statycznie, można pominąć sekcję.

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

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>Uaktualnianie (migrowanie) klasycznej Zastrzeżony adres IP statycznego publicznego adresu IP

Aby skorzystać z nowych możliwości usługi Azure Resource Manager, można przeprowadzić migrację istniejącego publicznego statycznego adresu IP — nazywanego zastrzeżonym adresem IP — z modelu klasycznego do nowoczesnego Azure Resource Manager ip.  Zmigrowany publiczny adres IP będzie typem podstawowej wersji SKU.


---

# <a name="reserved-to-basic---powershell"></a>[**Zarezerwowane dla wersji Podstawowa — PowerShell**](#tab/option-migrate-powershell)

W poniższym przykładzie przyjęto założenie, że poprzednie utworzenie klasycznej usługi Azure Zastrzeżony adres IP **myReservedIP** w **grupie myResourceGroup.** Innym wymaganiem wstępnym migracji jest upewninie się, Azure Resource Manager została zarejestrowana do migracji. Opisano to szczegółowo w krokach 3 i 4 [tej strony.](../virtual-machines/migration-classic-resource-manager-ps.md)

Aby zmigrować Zastrzeżony adres IP, wykonaj poniższe polecenia przy użyciu programu PowerShell.  Jeśli adres IP nie jest skojarzony z żadnej usługi (poniżej znajduje się usługa o nazwie **myService),** ten krok można pominąć.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
Poprzednie polecenie wyświetla ostrzeżenia i błędy, które blokują migrację. Jeśli walidacja powiedzie się, możesz wykonać następujące kroki, aby przygotować i zatwierdzić migrację:
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
Nowa grupa zasobów w programie Azure Resource Manager jest tworzona przy użyciu nazwy zmigrowanych zasobów Zastrzeżony adres IP (w powyższym przykładzie byłaby to grupa zasobów **myReservedIP-Migrated).**

# <a name="reserved-to-basic---cli"></a>[**Zarezerwowane dla wersji Podstawowa — interfejs wiersza polecenia**](#tab/option-migrate-cli)

W poniższym przykładzie przyjęto założenie, że poprzednie utworzenie klasycznej usługi Azure Zastrzeżony adres IP **myReservedIP** w **grupie myResourceGroup.** Innym wymaganiem wstępnym migracji jest upewninie się, Azure Resource Manager została zarejestrowana do migracji. Opisano to szczegółowo w krokach 3 i 4 [tej strony.](../virtual-machines/migration-classic-resource-manager-cli.md)

Aby zmigrować Zastrzeżony adres IP, wykonaj poniższe polecenia przy użyciu interfejsu wiersza polecenia platformy Azure.  Jeśli adres IP nie jest skojarzony z żadnej usługi (poniżej znajduje się usługa o nazwie **myService** i wdrożenie **myDeployment),** ten krok można pominąć.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
Poprzednie polecenie wyświetla ostrzeżenia i błędy, które blokują migrację. Jeśli walidacja powiedzie się, możesz wykonać następujące kroki, aby przygotować i zatwierdzić migrację:
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
Nowa grupa zasobów w programie Azure Resource Manager jest tworzona przy użyciu nazwy zmigrowanych zasobów Zastrzeżony adres IP (w powyższym przykładzie byłaby to grupa zasobów **myReservedIP-Migrated).**

---

## <a name="limitations"></a>Ograniczenia

* W celu uaktualnienia podstawowego publicznego adresu IP nie można go skojarzyć z żadnym zasobem platformy Azure.  Zapoznaj się [z tą stroną,](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) aby uzyskać więcej informacji na temat sposobu skojarzenia publicznych ip.  Podobnie, aby przeprowadzić migrację Zastrzeżony adres IP, nie można jej skojarzyć z żadnymi usługami w chmurze.  Zapoznaj się [z tą stroną,](./remove-public-ip-address-vm.md) aby uzyskać więcej informacji na temat sposobu skojarzenia zastrzeżonych ip.  
* Publiczne ip uaktualnione z podstawowej do standardowej wersji SKU nadal nie mają gwarantowanych [stref dostępności.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)  Upewnij się, że o tym pamiętać podczas wybierania zasobów, z którymi chcesz skojarzyć adres IP.
* Nie można zmienić wersji standardowej na podstawową.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [publicznych adresach IP na](./public-ip-addresses.md#public-ip-addresses) platformie Azure, w tym o różnicach między typami SKU, a także o [ustawieniach publicznych adresów IP.](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Dowiedz się, jak [uaktualnić publiczne usługi Równoważenie obciążenia Azure z poziomu podstawowego do standardowego.](../load-balancer/upgrade-basic-standard.md)
- Understand [Classic Azure Reserved IPPs](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) and Migration of Classic resources to Azure Resource Manager (Opis klasycznych zastrzeżonych ip platformy Azure i migracji zasobów klasycznych [do Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)).
