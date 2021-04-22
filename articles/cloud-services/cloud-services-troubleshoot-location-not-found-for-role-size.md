---
title: Rozwiązywanie problemów z rozmiarem LocationNotFoundForRoleSize podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure | Microsoft Docs
description: W tym artykule pokazano, jak rozwiązać wyjątek LocationNotFoundForRoleSize podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 54af2387ec0ff6c8f86f96821baad17736e8d85b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877970"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Rozwiązywanie problemów z rozmiarem LocationNotFoundForRoleSize podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure

W tym artykule rozwiążesz problemy z błędami alokacji, gdy rozmiar maszyny wirtualnej nie jest dostępny podczas wdrażania usługi w chmurze platformy Azure (wersja klasyczna).

Podczas wdrażania wystąpień w usłudze w chmurze (klasycznej) lub dodawania nowych wystąpień roli sieć Web lub proces roboczy Microsoft Azure zasoby obliczeniowe.

Czasami możesz otrzymywać błędy podczas tych operacji nawet przed osiągnięciem limitu subskrypcji platformy Azure.

> [!TIP]
> Informacje te mogą być również przydatne podczas planowania wdrożenia usług.

## <a name="symptom"></a>Objaw

W Azure Portal przejdź do usługi w chmurze (klasycznej), a następnie na pasku bocznym wybierz pozycję *Dziennik operacji (klasyczny),* aby wyświetlić dzienniki.

![Obraz przedstawia blok Dziennik operacji (klasyczny).](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

Podczas inspekcji dzienników usługi w chmurze (klasycznej) zostanie wyświetlony następujący wyjątek:

|Typ wyjątku  |Komunikat o błędzie  |
|---------|---------|
|LocationNotFoundForRoleSize     |Operacja `{Operation ID}` "" nie powiodła się: "Żądana warstwa maszyny wirtualnej nie jest obecnie dostępna w regionie ( `{Region ID}` ) dla tej subskrypcji. Wypróbuj inną warstwę lub wd wdrażaj w innej lokalizacji.".|

## <a name="cause"></a>Przyczyna

Występuje problem z pojemnością regionu lub klastra, w których wdrażasz. Wyjątek *LocationNotFoundForRoleSize* występuje, gdy wybrana przez Ciebie wartość SKU zasobu (rozmiar maszyny wirtualnej) nie jest dostępna dla określonego regionu.

## <a name="solution"></a>Rozwiązanie

W tym scenariuszu należy wybrać inny region lub inną pozycję SKU, w którym ma zostać wdrożona usługa w chmurze (klasyczna). Przed wdrożeniem lub uaktualnieniem usługi w chmurze (klasycznej) można określić, które jednostki SKU są dostępne w regionie lub strefie dostępności. Postępuj zgodnie z [poniższymi procesami interfejsu wiersza](#list-skus-in-region-using-azure-cli)polecenia platformy Azure, programu [PowerShell](#list-skus-in-region-using-powershell) [lub interfejsu API REST.](#list-skus-in-region-using-rest-api)

### <a name="list-skus-in-region-using-azure-cli"></a>Lista jednostki SKU w regionie przy użyciu interfejsu wiersza polecenia platformy Azure

Możesz użyć polecenia [az vm list-skus](/cli/azure/vm?view=azure-cli-latest
#<a name="az_vm_list_skus-command"></a>az_vm_list_skus).

- Użyj `--location` parametru , aby odfiltrować dane wyjściowe do lokalizacji, z których korzystasz.
- Użyj `--size` parametru , aby wyszukać według nazwy o częściowym rozmiarze.
- Aby uzyskać więcej informacji, zobacz przewodnik Rozwiązywanie błędów [dotyczących niedostępnej wersji SKU.](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli)

    **Przykład:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Przykładowe wyniki:** ![ Dane wyjściowe interfejsu wiersza polecenia platformy Azure z poleceniem "az vm list-skus --location southcentralus --size Standard_F --output table", które pokazuje dostępne jednostki SKU.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Lista jednostki SKU w regionie przy użyciu programu PowerShell

Możesz użyć polecenia [Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku)

- Przefiltruj wyniki według lokalizacji.
- Dla tego polecenia musi być dostępna najnowsza wersja programu PowerShell.
- Aby uzyskać więcej informacji, zobacz przewodnik Rozwiązywanie błędów [dotyczących niedostępnej wersji SKU.](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell)

**Przykład:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Niektóre inne przydatne polecenia:**

Odfiltruj lokalizacje zawierające rozmiar (Standard_DS14_v2):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Odfiltruj wszystkie lokalizacje zawierające rozmiar (V3):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>Lista jednostki SKU w regionie przy użyciu interfejsu API REST

Możesz użyć operacji [Resource Skus - List.](/rest/api/compute/resourceskus/list) Zwraca dostępne jednostki SKU i regiony w następującym formacie:

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

Aby uzyskać więcej rozwiązań błędów alokacji i lepiej zrozumieć sposób ich generowania:

> [!div class="nextstepaction"]
> [Błędy alokacji — usługa w chmurze (klasyczna)](cloud-services-allocation-failures.md)

Jeśli problem z platformą Azure nie został rozwiązany w tym artykule, odwiedź fora platformy Azure w [witrynie MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Swój problem możesz opublikować na tych forach lub opublikować w [ @AzureSupport serwisie Twitter.](https://twitter.com/AzureSupport) Możesz również przesłać wniosek o pomoc techniczną platformy Azure. Aby przesłać wniosek o pomoc techniczną, na stronie pomocy technicznej [platformy Azure](https://azure.microsoft.com/support/options/) wybierz pozycję Uzyskaj *pomoc techniczną.*
