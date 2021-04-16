---
title: Błędy niedostępnej wersji SKU
description: Opisuje sposób rozwiązywania problemów z błędem Niedostępny sku podczas wdrażania zasobów za pomocą Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 04/14/2021
ms.openlocfilehash: 3baedf6a5c9f2dbfd3ddf666b458fac649fce2ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503902"
---
# <a name="resolve-errors-for-sku-not-available"></a>Usuwanie błędów związanych z niedostępną jednostką SKU

W tym artykule opisano sposób rozwiązywania **błędu SkuNotAvailable.** Jeśli nie możesz znaleźć odpowiedniej sku w tym regionie/strefie lub alternatywnym regionie/strefie, która spełnia Twoje potrzeby biznesowe, prześlij żądanie [SKU](/troubleshoot/azure/general/region-access-request-process) do pomoc techniczna platformy Azure.

## <a name="symptom"></a>Objaw

Podczas wdrażania zasobu (zazwyczaj maszyny wirtualnej) jest wyświetlany następujący kod błędu i komunikat o błędzie:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy wybrana przez Ciebie wartość SKU zasobu (na przykład rozmiar maszyny wirtualnej) nie jest dostępna dla wybranej lokalizacji.

Jeśli wdrażasz maszynę wirtualną usługi Azure Spot lub wystąpienie zestawu skalowania spot, w tej lokalizacji nie ma żadnej pojemności dla usługi Azure Spot. Aby uzyskać więcej informacji, zobacz [Spot error messages (Komunikaty o błędach punktowych).](../../virtual-machines/error-codes-spot.md)

## <a name="solution-1---powershell"></a>Rozwiązanie 1 — PowerShell

Aby określić, które jednostki SKU są dostępne w regionie/strefie, użyj [polecenia Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku) Przefiltruj wyniki według lokalizacji. Musisz mieć najnowszą wersję programu PowerShell dla tego polecenia.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Wyniki zawierają listę jednostki SKU dla lokalizacji i wszelkie ograniczenia dotyczące tej jednostki SKU. Zwróć uwagę, że jako sku może być wymieniona `NotAvailableForSubscription` .

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Aby filtrować według lokalizacji i sku, użyj:

```azurepowershell-interactive
$SubId = (Get-AzContext).Subscription.Id

$Region = "centralus" # change region here
$VMSku = "Standard_M" # change VM SKU here

$VMSKUs = Get-AzComputeResourceSku | where {$_.Locations.Contains($Region) -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains($VMSku)}

$OutTable = @()

foreach ($SkuName in $VMSKUs.Name)
        {
            $LocRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Location")){"NotAvavalableInRegion"}else{"Available - No region restrictions applied" }
            $ZoneRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Zone")){"NotAvavalableInZone: "+(((($VMSKUs | where Name -EQ $SkuName).Restrictions.RestrictionInfo.Zones)| Where-Object {$_}) -join ",")}else{"Available - No zone restrictions applied"}
            
            
            $OutTable += New-Object PSObject -Property @{
                                                         "Name" = $SkuName
                                                         "Location" = $Region
                                                         "Applies to SubscriptionID" = $SubId
                                                         "Subscription Restriction" = $LocRestriction
                                                         "Zone Restriction" = $ZoneRestriction
                                                         }
         }

$OutTable | select Name, Location, "Applies to SubscriptionID", "Region Restriction", "Zone Restriction" | Sort-Object -Property Name | FT
```

Polecenie zwraca wyniki, takie jak:

```output
Name                   Location  Applies to SubscriptionID            Region Restriction                         Zone Restriction                        
----                   --------  -------------------------            ------------------------                   ----------------     
Standard_M128          centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-32ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-64ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128dms_v2    centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx NotAvavalableInRegion                      NotAvavalableInZone: 1,2,3
```

## <a name="solution-2---azure-cli"></a>Rozwiązanie 2 — interfejs wiersza polecenia platformy Azure

Aby określić, które jednostki SKU są dostępne w regionie, użyj [polecenia az vm list-skus.](/cli/azure/vm#az_vm_list_skus) Użyj `--location` parametru , aby filtrować dane wyjściowe według lokalizacji. Użyj `--size` parametru , aby wyszukać według częściowej nazwy rozmiaru. Użyj `--all` parametru , aby wyświetlić wszystkie informacje, w tym rozmiary, które nie są dostępne dla bieżącej subskrypcji.

Musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.15.0 lub nowszej. Aby sprawdzić swoją wersję, `az --version` użyj . W razie potrzeby [zaktualizuj instalację programu](/cli/azure/update-azure-cli).

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --all --output table
```

Polecenie zwraca wyniki takie jak:

```output
ResourceType     Locations       Name              Zones    Restrictions
---------------  --------------  ----------------  -------  --------------
virtualMachines  southcentralus  Standard_F1       1,2,3    None
virtualMachines  southcentralus  Standard_F2       1,2,3    None
virtualMachines  southcentralus  Standard_F4       1,2,3    None
...
virtualMachines  southcentralus  Standard_F72s_v2  1,2,3    NotAvailableForSubscription, type: Zone, locations: southcentralus, zones: 1,2,3
...
```

## <a name="solution-3---azure-portal"></a>Rozwiązanie 3 — Azure Portal

Aby określić, które jednostki SKU są dostępne w regionie, użyj [portalu](https://portal.azure.com). Zaloguj się do portalu i dodaj zasób za pośrednictwem interfejsu . Po skonfigurowaniu wartości zobaczysz dostępne jednostki SKU dla tego zasobu. Nie musisz kończyć wdrażania.

Na przykład rozpocznij proces tworzenia maszyny wirtualnej. Aby wyświetlić inny dostępny rozmiar, wybierz **pozycję Zmień rozmiar**.

![Tworzenie maszyny wirtualnej](./media/error-sku-not-available/create-vm.png)

Dostępne rozmiary można filtrować i przewijać.

![Dostępne jednostki SKU](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Rozwiązanie 4 — REST

Aby określić, które jednostki SKU są dostępne w regionie, użyj operacji [Resource Skus - List.](/rest/api/compute/resourceskus/list)

Zwraca dostępne jednostki SKU i regiony w następującym formacie:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```