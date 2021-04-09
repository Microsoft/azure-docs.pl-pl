---
title: Rozwiązywanie problemów z LocationNotFoundForRoleSize podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure | Microsoft Docs
description: W tym artykule opisano sposób rozwiązywania wyjątku LocationNotFoundForRoleSize podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 3d1a1135db0421d89d4c6c9f278c86e02cb5cb32
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558962"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Rozwiązywanie problemów z LocationNotFoundForRoleSize podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure

W tym artykule opisano Rozwiązywanie problemów z błędami alokacji, w których rozmiar maszyny wirtualnej (VM) nie jest dostępny podczas wdrażania usługi w chmurze platformy Azure (klasycznego).

W przypadku wdrażania wystąpień do usługi w chmurze (klasycznej) lub dodawania nowych wystąpień roli sieci Web lub procesu roboczego Microsoft Azure przydziela zasoby obliczeniowe.

Czasami możesz otrzymywać błędy podczas tych operacji nawet przed osiągnięciem limitu subskrypcji platformy Azure.

> [!TIP]
> Te informacje mogą być również przydatne podczas planowania wdrożenia usług.

## <a name="symptom"></a>Objaw

W Azure Portal przejdź do usługi w chmurze (klasycznej), a następnie na pasku bocznym wybierz pozycję *dziennik operacji (klasyczny)* , aby wyświetlić dzienniki.

![Obraz przedstawia blok dziennik operacji (klasyczny).](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

Podczas inspekcji dzienników usługi w chmurze (klasycznej) zobaczysz następujący wyjątek:

|Typ wyjątku  |Komunikat o błędzie  |
|---------|---------|
|LocationNotFoundForRoleSize     |Operacja " `{Operation ID}` " nie powiodła się: "żądana warstwa maszyny wirtualnej jest obecnie niedostępna w regionie ( `{Region ID}` ) dla tej subskrypcji. Wypróbuj inną warstwę lub Wdróż ją w innej lokalizacji.|

## <a name="cause"></a>Przyczyna

Występuje problem z pojemnością w ramach tego regionu lub klastra, na którym jest wdrażana. Wyjątek *LocationNotFoundForRoleSize* występuje, gdy wybrana jednostka SKU zasobu (rozmiar maszyny wirtualnej) nie jest dostępna dla określonego regionu.

## <a name="solution"></a>Rozwiązanie

W tym scenariuszu należy wybrać inny region lub jednostkę SKU do wdrożenia usługi w chmurze (klasycznej) do programu. Przed wdrożeniem lub uaktualnieniem usługi w chmurze (klasyczną) można określić, które jednostki SKU są dostępne w obszarze regionu lub strefy dostępności. Postępuj zgodnie z poniższymi procesami interfejsu [wiersza polecenia platformy Azure](#list-skus-in-region-using-azure-cli), [programu PowerShell](#list-skus-in-region-using-powershell)lub [interfejsem API REST](#list-skus-in-region-using-rest-api) .

### <a name="list-skus-in-region-using-azure-cli"></a>Wyświetlanie listy jednostek SKU w regionie przy użyciu interfejsu wiersza polecenia platformy Azure

Możesz użyć polecenia [AZ VM list-SKU](/cli/azure/vm.html#az_vm_list_skus) .

- Użyj `--location` parametru, aby odfiltrować dane wyjściowe do lokalizacji, której używasz.
- Użyj `--size` parametru, aby wyszukać według częściowej nazwy rozmiaru.
- Aby uzyskać więcej informacji, zobacz temat [Rozwiązywanie problemów dotyczących niedostępnej jednostki SKU](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) .

    **Na przykład:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Przykładowe wyniki:** ![ W danych wyjściowych interfejsu wiersza polecenia platformy Azure można uruchomić polecenie "AZ VM list-SKU--Location southcentralus--size Standard_F--Output Table", które wyświetla dostępne jednostki SKU.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Wyświetlanie listy jednostek SKU w regionie przy użyciu programu PowerShell

Można użyć polecenia [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) .

- Filtruj wyniki według lokalizacji.
- Dla tego polecenia trzeba mieć najnowszą wersję programu PowerShell.
- Aby uzyskać więcej informacji, zobacz temat [Rozwiązywanie problemów dotyczących niedostępnej jednostki SKU](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) .

**Na przykład:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Inne przydatne polecenia:**

Odfiltruj lokalizacje, które zawierają rozmiar (Standard_DS14_v2):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Odfiltruj wszystkie lokalizacje, które zawierają rozmiar (V3):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>Wyświetlanie listy jednostek SKU w regionie przy użyciu interfejsu API REST

Możesz użyć operacji [SKU zasobów — lista](/rest/api/compute/resourceskus/list) . Zwraca dostępne jednostki SKU i regiony w następującym formacie:

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
      <<The rest of your file is located here>>
  ]
}
    
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej rozwiązań dotyczących błędów alokacji i lepiej zrozumieć, jak są generowane:

> [!div class="nextstepaction"]
> [Błędy alokacji — usługa w chmurze (klasyczna)](cloud-services-allocation-failures.md)

Jeśli problem z platformą Azure nie został rozwiązany w tym artykule, odwiedź fora platformy Azure w [witrynie MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Możesz ogłosić swój problem na tych forach lub opublikować go w serwisie [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Możesz również przesłać żądanie pomocy technicznej platformy Azure. Aby przesłać żądanie pomocy technicznej, na stronie [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) wybierz pozycję *Uzyskaj pomoc techniczną*.