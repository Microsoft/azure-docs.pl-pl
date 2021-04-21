---
title: Template deployment what-if
description: Określ, jakie zmiany zostaną wprowadzone w zasobach przed wdrożeniem Azure Resource Manager szablonu.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tomfitz
ms.openlocfilehash: 7e300f896bb11ed7c77738836f894cff41cc8bf3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781833"
---
# <a name="arm-template-deployment-what-if-operation"></a>Operacja analizy co-jeżeli wdrożenia szablonu usługi Resource Manager

Przed wdrożeniem szablonu Azure Resource Manager (szablonu usługi ARM) możesz wyświetlić podgląd zachodzjących zmian. Azure Resource Manager zawiera operację what-if, aby zobaczyć, jak zmienią się zasoby w przypadku wdrożenia szablonu. Operacja warunkowa nie wprowadza żadnych zmian w istniejących zasobach. Zamiast tego przewiduje zmiany w przypadku wdrożenia określonego szablonu.

Operacji what-if można użyć w przypadku operacji Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. What-if is supported for resource group, subscription, management group, and tenant level deployments.

## <a name="install-azure-powershell-module"></a>Instalowanie Azure PowerShell modułu

Aby użyć funkcji what-if w programie PowerShell, musisz mieć wersję **4.2** lub nowszą modułu Az.

Aby zainstalować moduł, użyj:

```powershell
Install-Module -Name Az -Force
```

Aby uzyskać więcej informacji na temat instalowania modułów, [zobacz Instalowanie Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="install-azure-cli-module"></a>Instalowanie modułu interfejsu wiersza polecenia platformy Azure

Aby korzystać z funkcji what-if w interfejsie wiersza polecenia platformy Azure, musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.14.0 lub nowszej. W razie potrzeby [zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Wyświetlanie wyników

Jeśli używasz analizy what-if w programie PowerShell lub interfejsie wiersza polecenia platformy Azure, dane wyjściowe zawierają wyniki zakodowane kolorami, które ułatwiają wyświetlanie różnych typów zmian.

![Resource Manager szablonu operacji what-if fullresourcepayload i zmień typy](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Dane wyjściowe tekstu są:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

> [!NOTE]
> Operacja what-if nie może rozpoznać funkcji [odwołania](template-functions-resource.md#reference). Za każdym razem, gdy właściwość jest ustawiana na wyrażenie szablonu, które zawiera funkcję odwołania, raporty what-if będą zmieniać właściwość. To zachowanie występuje, ponieważ wyrażenie what-if porównuje bieżącą wartość właściwości (na przykład lub dla wartości logicznych) z `true` `false` nierozpoznaną wartością wyrażenia szablonu. Oczywiście te wartości nie będą zgodne. Podczas wdrażania szablonu właściwość zmieni się tylko wtedy, gdy wyrażenie szablonu zostanie rozwiązane do innej wartości.

## <a name="what-if-commands"></a>Polecenia what-if

### <a name="azure-powershell"></a>Azure PowerShell

Aby wyświetlić podgląd zmian przed wdrożeniem szablonu, użyj [new-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) lub [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment). Dodaj parametr `-Whatif` switch do polecenia wdrożenia.

* `New-AzResourceGroupDeployment -Whatif` dla wdrożeń grupy zasobów
* `New-AzSubscriptionDeployment -Whatif` i `New-AzDeployment -Whatif` dla wdrożeń na poziomie subskrypcji

Możesz użyć parametru switch, aby wyświetlić podgląd zmian i wyświetlić monit o `-Confirm` kontynuowanie wdrażania.

* `New-AzResourceGroupDeployment -Confirm` dla wdrożeń grupy zasobów
* `New-AzSubscriptionDeployment -Confirm` i `New-AzDeployment -Confirm` dla wdrożeń na poziomie subskrypcji

Poprzednie polecenia zwracają tekstowe podsumowanie, które można ręcznie sprawdzić. Aby uzyskać obiekt, który można programowo sprawdzić pod celu zmiany, użyj [get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) lub [Get-AzSubscriptionDeploymentWhatIfResult.](/powershell/module/az.resources/get-azdeploymentwhatifresult)

* `$results = Get-AzResourceGroupDeploymentWhatIfResult` dla wdrożeń grupy zasobów
* `$results = Get-AzSubscriptionDeploymentWhatIfResult` lub `$results = Get-AzDeploymentWhatIfResult` w przypadku wdrożeń na poziomie subskrypcji

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wyświetlić podgląd zmian przed wdrożeniem szablonu, użyj:

* [az deployment group what-if](/cli/azure/deployment/group#az_deployment_group_what_if) for resource group deployments
* [az deployment sub what-if](/cli/azure/deployment/sub#az_deployment_sub_what_if) for subscription level deployments
* [az deployment mg what-if](/cli/azure/deployment/mg#az_deployment_mg_what_if) for management group deployments
* [az deployment tenant what-if](/cli/azure/deployment/tenant#az_deployment_tenant_what_if) for tenant deployments

Możesz użyć przełącznika (lub jego krótkiej formy), aby wyświetlić podgląd zmian i wyświetlić monit o `--confirm-with-what-if` `-c` kontynuowanie wdrażania. Dodaj ten przełącznik do:

* [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)
* [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create).
* [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create)
* [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create)

Na przykład użyj lub `az deployment group create --confirm-with-what-if` `-c` dla wdrożeń grupy zasobów.

Poprzednie polecenia zwracają tekstowe podsumowanie, które można ręcznie sprawdzić. Aby uzyskać obiekt JSON, który można programowo sprawdzić pod względu na zmiany, użyj `--no-pretty-print` przełącznika . Na przykład użyj `az deployment group what-if --no-pretty-print` dla wdrożeń grupy zasobów.

Jeśli chcesz zwrócić wyniki bez kolorów, otwórz plik konfiguracji [interfejsu wiersza polecenia platformy Azure.](/cli/azure/azure-cli-configuration) Ustaw **no_color** wartość **tak.**

### <a name="azure-rest-api"></a>Interfejs API REST platformy Azure

W przypadku interfejsu API REST użyj:

* [Wdrożenia — What If](/rest/api/resources/deployments/whatif) wdrożeń grup zasobów
* [Wdrożenia — What If w zakresie subskrypcji dla](/rest/api/resources/deployments/whatifatsubscriptionscope) wdrożeń subskrypcji
* [Wdrożenia — What If w zakresie grupy zarządzania](/rest/api/resources/deployments/whatifatmanagementgroupscope) dla wdrożeń grup zarządzania
* [Wdrożenia — What If w zakresie dzierżawy](/rest/api/resources/deployments/whatifattenantscope) dla wdrożeń dzierżawy.

## <a name="change-types"></a>Typy zmian

Operacja what-if zawiera sześć różnych typów zmian:

- **Utwórz:** zasób nie istnieje obecnie, ale jest zdefiniowany w szablonie. Zasób zostanie utworzony.

- **Usuń:** ten typ zmiany ma zastosowanie tylko w przypadku [używania trybu pełnego](deployment-modes.md) do wdrożenia. Zasób istnieje, ale nie jest zdefiniowany w szablonie. W przypadku trybu pełnego zasób zostanie usunięty. Ten typ zmiany [obejmuje tylko zasoby,](complete-mode-deletion.md) które obsługują usuwanie w trybie kompletnym.

- **Ignoruj:** zasób istnieje, ale nie jest zdefiniowany w szablonie. Zasób nie zostanie wdrożony ani zmodyfikowany.

- **NoChange:** zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie wdrożony ponownie, ale właściwości zasobu nie ulegną zmianie. Ten typ zmiany jest zwracany, gdy [dla właściwości ResultFormat](#result-format) ustawiono wartość `FullResourcePayloads` , która jest wartością domyślną.

- **Modify:** zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie wdrożony ponownie i właściwości zasobu ulegną zmianie. Ten typ zmiany jest zwracany, gdy [dla właściwości ResultFormat](#result-format) ustawiono wartość `FullResourcePayloads` , która jest wartością domyślną.

- **Wdrażanie:** zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie wdrożony ponownie. Właściwości zasobu mogą, ale nie muszą ulec zmianie. Operacja zwraca ten typ zmiany, jeśli nie ma wystarczających informacji, aby określić, czy jakieś właściwości ulegną zmianie. Ten warunek jest wyświetlony tylko wtedy, gdy [dla opcji ResultFormat](#result-format) ustawiono wartość `ResourceIdOnly` .

## <a name="result-format"></a>Format wyniku

Możesz kontrolować poziom szczegółowości zwracanych informacji o przewidywanych zmianach. Dostępne są dwie opcje:

* **FullResourcePayloads** — zwraca listę zasobów, które zostaną zmieniane, oraz szczegółowe informacje o właściwościach, które zostaną zmieniane
* **ResourceIdOnly** — zwraca listę zasobów, które zmienią się

Wartość domyślna to **FullResourcePayloads.**

W przypadku poleceń wdrażania programu PowerShell użyj `-WhatIfResultFormat` parametru . W poleceniach obiektu programowego użyj `ResultFormat` parametru .

W przypadku interfejsu wiersza polecenia platformy Azure użyj `--result-format` parametru .
 
Następujące wyniki pokazują dwa różne formaty danych wyjściowych:

- Pełne ładunki zasobów

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Tylko identyfikator zasobu

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Uruchamianie operacji what-if

### <a name="set-up-environment"></a>Konfigurowanie środowiska

Aby sprawdzić, jak działa działanie what-if, uruchomimy kilka testów. Najpierw należy wdrożyć [szablon, który tworzy sieć wirtualną](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Użyjesz tej sieci wirtualnej, aby przetestować sposób, w jaki zmiany są zgłaszane przez usługę what-if.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Modyfikacja testu

Po zakończeniu wdrażania możesz przetestować operację what-if. Tym razem wdrożysz [szablon, który zmienia sieć wirtualną](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Brakuje jednego z oryginalnych tagów, usunięto podsieć i zmieniono prefiks adresu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

Dane wyjściowe operacji what-if wyświetlane są podobnie do następujących:

![Resource Manager danych wyjściowych operacji what-if wdrożenia szablonu](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Dane wyjściowe tekstu to:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Zwróć uwagę, że w górnej części danych wyjściowych zdefiniowano kolory wskazujące typ zmian.

W dolnej części danych wyjściowych widać, że tag Właściciel został usunięty. Prefiks adresu został zmieniony z 10.0.0.0/16 na 10.0.0.0/15. Podsieć o nazwie subnet001 została usunięta. Pamiętaj, że te zmiany nie zostały wdrożone. Zostanie wyświetlony podgląd zmian, które zostaną wprowadzone w przypadku wdrożenia szablonu.

Niektóre właściwości, które są wyświetlane jako usunięte, w rzeczywistości nie zmienią się. Właściwości mogą być niepoprawnie zgłaszane jako usunięte, gdy nie znajdują się w szablonie, ale są automatycznie ustawiane podczas wdrażania jako wartości domyślne. Ten wynik jest uznawany za "szum" w odpowiedzi what-if. Ostateczny wdrożony zasób będzie miał ustawione wartości właściwości. Gdy operacja what-if dojrzeje, te właściwości zostaną odfiltrowane z wyniku.

## <a name="programmatically-evaluate-what-if-results"></a>Programowe ocenianie wyników analizy what-if

Teraz programowo oceńmy wyniki analizy what-if, ustawiając polecenie na zmienną.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Zostanie wyświetlony podsumowanie każdej zmiany.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Potwierdzenie usunięcia

Operacja what-if obsługuje używanie [trybu wdrażania](deployment-modes.md). Po skonfigurowaniu trybu ukończenia zasoby poza szablonem są usuwane. Poniższy przykład wdraża [szablon, który nie ma żadnych zasobów zdefiniowanych w](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) trybie kompletnym.

Aby wyświetlić podgląd zmian przed wdrożeniem szablonu, użyj parametru przełącznika potwierdzania w ramach polecenia wdrażania. Jeśli zmiany są zgodnie z oczekiwaniami, odpowiedz, że chcesz zakończyć wdrożenie.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Ponieważ w szablonie nie zdefiniowano żadnych zasobów, a tryb wdrażania został ustawiony na ukończenie, sieć wirtualna zostanie usunięta.

![Resource Manager wdrożenia szablonu, jeśli tryb wdrażania danych wyjściowych operacji jest ukończony](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Dane wyjściowe tekstu to:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001&quot;
      location:        &quot;centralus&quot;
      name:            &quot;vnet-001&quot;
      tags.CostCenter: &quot;12345&quot;
      tags.Owner:      &quot;Team A&quot;
      type:            &quot;Microsoft.Network/virtualNetworks&quot;

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is &quot;Y"):
```

Zobaczysz oczekiwane zmiany i potwierdzisz, że chcesz uruchomić wdrożenie.

## <a name="sdks"></a>Zestawy SDK

Możesz użyć operacji what-if za pośrednictwem zestawów Azure SDK.

* W przypadku języka Python użyj [what-if](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-).

* W przypadku języka Java użyj [klasy DeploymentWhatIf](/java/api/com.microsoft.azure.management.resources.deploymentwhatif).

* W przypadku programu .NET użyj [klasy DeploymentWhatIf](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif).

## <a name="next-steps"></a>Następne kroki

- Aby użyć operacji what-if w potoku, zobacz Testowanie szablonów arm z What-If [w potoku](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/).
- Jeśli zauważysz nieprawidłowe wyniki operacji analizy what-if, zgłoś problemy na stronie [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .
- Aby uzyskać Microsoft Learn, który obejmuje korzystanie z funkcji what if, zobacz Podgląd zmian i weryfikowanie zasobów platformy Azure przy użyciu funkcji what-if i zestawu narzędzi [testowych szablonu usługi ARM.](/learn/modules/arm-template-test/)
