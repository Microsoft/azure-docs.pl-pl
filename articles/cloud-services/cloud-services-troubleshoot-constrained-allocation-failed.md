---
title: Rozwiązywanie problemów z ConstrainedAllocationFailed podczas wdrażania usługi w chmurze na platformie Azure | Microsoft Docs
description: W tym artykule opisano sposób rozwiązywania wyjątku ConstrainedAllocationFailed podczas wdrażania usługi w chmurze na platformie Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: de344bbcd89158676bacf2a8aa1743d282700b9d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521071"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-to-azure"></a>Rozwiązywanie problemów z ConstrainedAllocationFailed podczas wdrażania usługi w chmurze na platformie Azure

W tym artykule opisano Rozwiązywanie problemów z błędami alokacji, w których usługa Azure Cloud Services nie może zostać wdrożona ze względu na ograniczenia.

Microsoft Azure przydzielane, gdy jesteś:

- Uaktualnianie wystąpień usług Cloud Services

- Dodawanie nowych wystąpień roli sieci Web lub procesu roboczego

- Wdrażanie wystąpień do usługi w chmurze

Czasami możesz otrzymywać błędy podczas tych operacji nawet przed osiągnięciem limitu subskrypcji platformy Azure.

> [!TIP]
> Te informacje mogą być również przydatne podczas planowania wdrożenia usług.

## <a name="symptom"></a>Objaw

W Azure Portal przejdź do usługi w chmurze, a następnie na pasku bocznym wybierz pozycję *dzienniki operacji (klasyczny)* , aby wyświetlić dzienniki.

Podczas inspekcji dzienników usługi w chmurze zobaczysz następujący wyjątek:

|Typ wyjątku  |Komunikat o błędzie  |
|---------|---------|
|ConstrainedAllocationFailed     |Operacja platformy Azure " `{Operation ID}` " nie powiodła się z kodem COMPUTE. ConstrainedAllocationFailed. Szczegóły: Alokacja nie powiodła się; nie można spełnić ograniczeń w żądaniu. Zażądane nowe wdrożenie usługi jest związane z grupą koligacji, dotyczy sieci wirtualnej albo w ramach tej usługi hostowanej występuje już wdrożenie. Każdy z tych warunków ogranicza nowe wdrożenie do określonych zasobów platformy Azure. Spróbuj ponownie później lub spróbuj zmniejszyć rozmiar maszyny wirtualnej lub liczbę wystąpień roli. Ewentualnie usuń w miarę możliwości wspomniane ograniczenia lub spróbuj wdrożyć w innym regionie.|

## <a name="cause"></a>Przyczyna

Występuje problem z pojemnością w ramach tego regionu lub klastra, na którym jest wdrażana. Występuje, gdy wybrana jednostka SKU zasobu nie jest dostępna dla określonej lokalizacji.

> [!NOTE]
> Gdy pierwszy węzeł usługi w chmurze zostanie wdrożony, zostanie on *przypięty* do puli zasobów. Pula zasobów może być pojedynczym klastrem lub grupą klastrów.
>
> W miarę upływu czasu zasoby w tej puli zasobów mogą być w pełni wykorzystane. Jeśli usługa w chmurze wykonuje żądanie alokacji dodatkowych zasobów, gdy w przypiętej puli zasobów są dostępne niewystarczające zasoby, żądanie spowoduje [Niepowodzenie alokacji](cloud-services-allocation-failures.md).

## <a name="solution"></a>Rozwiązanie

W tym scenariuszu należy wybrać inny region lub jednostkę SKU, w której ma zostać wdrożona usługa w chmurze. Przed wdrożeniem lub uaktualnieniem usługi w chmurze możesz określić, które jednostki SKU są dostępne w obszarze regionu lub strefy dostępności. Postępuj zgodnie z poniższymi procesami interfejsu [wiersza polecenia platformy Azure](#list-skus-in-region-using-azure-cli), [programu PowerShell](#list-skus-in-region-using-powershell)lub [interfejsem API REST](#list-skus-in-region-using-rest-api) .

### <a name="list-skus-in-region-using-azure-cli"></a>Wyświetlanie listy jednostek SKU w regionie przy użyciu interfejsu wiersza polecenia platformy Azure

Możesz użyć polecenia [AZ VM list-SKU](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus) .

- Użyj `--location` parametru, aby odfiltrować dane wyjściowe do lokalizacji, której używasz.
- Użyj `--size` parametru, aby wyszukać według częściowej nazwy rozmiaru.
- Aby uzyskać więcej informacji, zobacz temat [Rozwiązywanie problemów dotyczących niedostępnej jednostki SKU](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) .

    **Na przykład:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Przykładowe wyniki:** ![ W danych wyjściowych interfejsu wiersza polecenia platformy Azure można uruchomić polecenie "AZ VM list-SKU--Location southcentralus--size Standard_F--Output Table", które wyświetla dostępne jednostki SKU.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Wyświetlanie listy jednostek SKU w regionie przy użyciu programu PowerShell

Można użyć polecenia [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) .

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

Możesz użyć operacji [SKU zasobów — lista](https://docs.microsoft.com/rest/api/compute/resourceskus/list) . Zwraca dostępne jednostki SKU i regiony w następującym formacie:

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
    <Rest_of_your_file_is_located_here...>
  ]
}
    
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej rozwiązań dotyczących błędów alokacji i lepiej zrozumieć, jak są generowane:

> [!div class="nextstepaction"]
> [Błędy alokacji (Cloud Services)](cloud-services-allocation-failures.md)

Jeśli problem z platformą Azure nie został rozwiązany w tym artykule, odwiedź fora platformy Azure w [witrynie MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Możesz ogłosić swój problem na tych forach lub opublikować go w serwisie [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Możesz również przesłać żądanie pomocy technicznej platformy Azure. Aby przesłać żądanie pomocy technicznej, na stronie [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) wybierz pozycję *Uzyskaj pomoc techniczną*.
