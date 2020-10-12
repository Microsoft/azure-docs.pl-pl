---
title: Template deployment co zrobić (wersja zapoznawcza)
description: Przed wdrożeniem szablonu Azure Resource Manager Ustal, jakie zmiany będą miały miejsce w swoich zasobach.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: tomfitz
ms.openlocfilehash: 27efe1e03b8a0d373d566106a53a41007731973e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87810075"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Wdrażanie szablonu ARM — operacja, którą należy wykonać (wersja zapoznawcza)

Przed wdrożeniem szablonu Azure Resource Manager (szablon ARM) można wyświetlić podgląd zmian, które zostaną wykonane. Azure Resource Manager zapewnia operację działania warunkowego, która pozwala zobaczyć, jak zasoby zmienią się w przypadku wdrożenia szablonu. Operacja działania warunkowego nie wprowadza żadnych zmian w istniejących zasobach. Zamiast tego przewiduje zmiany w przypadku wdrożenia określonego szablonu.

> [!NOTE]
> Operacja działania warunkowego jest obecnie w wersji zapoznawczej. W wersji zapoznawczej wyniki mogą czasami wskazywać, że zasób ulegnie zmianie, gdy nie zostanie wykonana żadna zmiana. Pracujemy nad zmniejszeniem tych problemów, ale potrzebujemy pomocy. Zgłoś te problemy pod adresem [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .

Operacji działania warunkowego można użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub operacji interfejsu API REST. Co to jest obsługiwane dla wdrożeń grupy zasobów, subskrypcji, grupy zarządzania i na poziomie dzierżawy.

## <a name="install-azure-powershell-module"></a>Zainstaluj moduł Azure PowerShell

Aby użyć tego, co jest dostępne w programie PowerShell, musisz mieć wersję **4,2 lub nowszą w programie AZ module**.

Jednak przed zainstalowaniem wymaganego modułu upewnij się, że masz rdzeń programu PowerShell (6. x lub 7. x). Jeśli masz program PowerShell 5. x lub starszy, [Zaktualizuj swoją wersję programu PowerShell](/powershell/scripting/install/installing-powershell). Nie można zainstalować wymaganego modułu w programie PowerShell 5. x lub starszym.

### <a name="install-latest-version"></a>Zainstaluj najnowszą wersję

Aby zainstalować moduł, użyj:

```powershell
Install-Module -Name Az -Force
```

Aby uzyskać więcej informacji o instalowaniu modułów, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="uninstall-alpha-version"></a>Odinstaluj wersję Alpha

Jeśli wcześniej zainstalowano wersję Alpha modułu warunkowego, należy odinstalować ten moduł. Wersja Alpha była dostępna tylko dla użytkowników, którzy zarejestrowali się w celu uzyskania wczesnej wersji zapoznawczej. Jeśli ta wersja zapoznawcza nie została zainstalowana, można pominąć tę sekcję.

1. Uruchom program PowerShell jako administrator.
1. Sprawdź zainstalowane wersje modułu AZ. resources.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Jeśli masz zainstalowaną wersję z numerem wersji w formacie **2. x. x-Alpha**, Odinstaluj tę wersję.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Wyrejestrowanie repozytorium warunkowego, które zostało użyte do zainstalowania wersji zapoznawczej.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

Wszystko jest gotowe do użycia.

## <a name="install-azure-cli-module"></a>Instalowanie modułu interfejsu wiersza polecenia platformy Azure

Aby użyć tego, co jest dostępne w interfejsie wiersza polecenia platformy Azure, musisz mieć interfejs wiersza polecenia platformy Azure 2.5.0 lub nowszy. W razie konieczności [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Zobacz wyniki

W przypadku korzystania z tego, co w programie PowerShell lub interfejsu wiersza polecenia platformy Azure, dane wyjściowe zawierają wyniki kodowane kolorami, które pomagają zobaczyć różne typy zmian.

![Wdrożenie szablonu Menedżer zasobów operacji fullresourcepayload i typów zmian](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Tekst wyjściowy to:

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
> Operacja działania warunkowego nie może rozpoznać [funkcji referencyjnej](template-functions-resource.md#reference). Za każdym razem, gdy ustawiasz właściwość na wyrażenie szablonu, które zawiera funkcję referencyjną, raporty co do tego, że właściwość zostanie zmieniona. Takie zachowanie ma miejsce, ponieważ porównywanie bieżącej wartości właściwości ( `true` `false` na przykład wartości logicznej) z nierozpoznanym wyrażeniem szablonu. Oczywiście te wartości nie są zgodne. Podczas wdrażania szablonu Właściwość zmieni się tylko wtedy, gdy wyrażenie szablonu zostanie rozwiązany na inną wartość.

## <a name="what-if-commands"></a>Polecenia warunkowe

### <a name="azure-powershell"></a>Azure PowerShell

Aby wyświetlić podgląd zmian przed wdrożeniem szablonu, użyj polecenie [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) lub [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment). Dodaj `-Whatif` parametr Switch do polecenia Deployment.

* `New-AzResourceGroupDeployment -Whatif` dla wdrożeń grup zasobów
* `New-AzSubscriptionDeployment -Whatif` i `New-AzDeployment -Whatif` dla wdrożeń na poziomie subskrypcji

Możesz użyć `-Confirm` parametru Switch, aby wyświetlić podgląd zmian i uzyskać monit o kontynuowanie wdrożenia.

* `New-AzResourceGroupDeployment -Confirm` dla wdrożeń grup zasobów
* `New-AzSubscriptionDeployment -Confirm` i `New-AzDeployment -Confirm` dla wdrożeń na poziomie subskrypcji

Powyższe polecenia zwracają podsumowanie tekstu, które można ręcznie sprawdzić. Aby uzyskać obiekt, który można programowo sprawdzić pod kątem zmian, użyj polecenie [Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) lub [Get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult).

* `$results = Get-AzResourceGroupDeploymentWhatIfResult` dla wdrożeń grup zasobów
* `$results = Get-AzSubscriptionDeploymentWhatIfResult` lub `$results = Get-AzDeploymentWhatIfResult` w przypadku wdrożeń na poziomie subskrypcji

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wyświetlić podgląd zmian przed wdrożeniem szablonu, użyj:

* [AZ Deployment Group to-If](/cli/azure/deployment/group#az-deployment-group-what-if) dla wdrożeń grupy zasobów
* [AZ Deployment sub-if](/cli/azure/deployment/sub#az-deployment-sub-what-if) w przypadku wdrożeń na poziomie subskrypcji
* [AZ Deployment mg-if](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-what-if) for Management Group Deployments
* [AZ Deployment dzierżawca-if](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-what-if) for dzierżawca

Możesz użyć `--confirm-with-what-if` przełącznika (lub jego krótkiej formy `-c` ), aby wyświetlić podgląd zmian i uzyskać monit o kontynuowanie wdrożenia. Dodaj ten przełącznik do:

* [AZ Deployment Group Create](/cli/azure/deployment/group#az-deployment-group-create)
* [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create).
* [AZ Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create)
* [AZ Deployment dzierżawca Create](/cli/azure/deployment/tenant#az-deployment-tenant-create)

Na przykład użyj `az deployment group create --confirm-with-what-if` lub `-c` dla wdrożeń grup zasobów.

Powyższe polecenia zwracają podsumowanie tekstu, które można ręcznie sprawdzić. Aby uzyskać obiekt JSON, który można programowo sprawdzić pod kątem zmian, użyj `--no-pretty-print` przełącznika. Na przykład w `az deployment group what-if --no-pretty-print` przypadku wdrożeń grup zasobów.

Jeśli chcesz zwrócić wyniki bez kolorów, Otwórz plik [konfiguracji interfejsu wiersza polecenia platformy Azure](/cli/azure/azure-cli-configuration) . Ustaw wartość **no_color** na **tak**.

### <a name="azure-rest-api"></a>Interfejs API REST platformy Azure

W przypadku interfejsu API REST wykonaj następujące czynności:

* [Wdrożenia — What If](/rest/api/resources/deployments/whatif) do wdrożeń grup zasobów
* [Wdrożenia — What If w zakresie subskrypcji](/rest/api/resources/deployments/whatifatsubscriptionscope) dla wdrożeń subskrypcji
* [Wdrożenia — What If w zakresie grupy zarządzania](/rest/api/resources/deployments/whatifatmanagementgroupscope) dla wdrożeń grup zarządzania
* [Wdrożenia — What If w zakresie dzierżawy](/rest/api/resources/deployments/whatifattenantscope) dla wdrożeń dzierżawy.

## <a name="change-types"></a>Zmień typy

Operacja działania warunkowego zawiera listę sześciu różnych typów zmian:

- **Utwórz**: zasób nie istnieje, ale jest zdefiniowany w szablonie. Zasób zostanie utworzony.

- **Usuń**: ten typ zmiany stosuje się tylko w przypadku korzystania z [trybu kompletnego](deployment-modes.md) dla wdrożenia. Zasób istnieje, ale nie jest zdefiniowany w szablonie. W przypadku trybu kompletnego zasób zostanie usunięty. Ten typ zmiany obejmuje tylko zasoby [obsługujące usuwanie w trybie pełnym](complete-mode-deletion.md) .

- **Ignoruj**: zasób istnieje, ale nie jest zdefiniowany w szablonie. Zasób nie zostanie wdrożony ani zmodyfikowany.

- **NOCHANGE**: zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie wdrożony ponownie, ale właściwości zasobu nie ulegną zmianie. Ten typ zmiany jest zwracany, gdy [ResultFormat](#result-format) jest ustawiona na `FullResourcePayloads` wartość, która jest wartością domyślną.

- **Modyfikacja**: zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie wdrożony ponownie, a właściwości zasobu zmienią się. Ten typ zmiany jest zwracany, gdy [ResultFormat](#result-format) jest ustawiona na `FullResourcePayloads` wartość, która jest wartością domyślną.

- **Wdróż**: zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie wdrożony ponownie. Właściwości zasobu mogą lub nie mogą się zmieniać. Operacja zwraca ten typ zmiany, jeśli nie ma wystarczających informacji, aby określić, czy zmiany zostaną zmienione. Ten warunek jest wyświetlany tylko wtedy, gdy [ResultFormat](#result-format) jest ustawiony na `ResourceIdOnly` .

## <a name="result-format"></a>Format wyniku

Kontrolujesz poziom szczegółowości zwracanych informacji o przewidywanych zmianach. Dostępne są dwie opcje:

* **FullResourcePayloads** — zwraca listę zasobów, które zostaną zmienione i szczegółowe informacje o właściwościach, które zostaną zmienione
* **ResourceIdOnly** — zwraca listę zasobów, które zostaną zmienione

Wartość domyślna to **FullResourcePayloads**.

W przypadku poleceń wdrażania programu PowerShell należy użyć `-WhatIfResultFormat` parametru. W poleceniach obiektów programistycznych należy użyć `ResultFormat` parametru.

W przypadku interfejsu wiersza polecenia platformy Azure Użyj `--result-format` parametru.
 
Poniższe Wyniki pokazują dwa różne formaty danych wyjściowych:

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

## <a name="run-what-if-operation"></a>Uruchom operację wykonywania czynności

### <a name="set-up-environment"></a>Konfigurowanie środowiska

Aby zobaczyć, jak działa działanie, Uruchommy kilka testów. Najpierw Wdróż [szablon, który tworzy sieć wirtualną](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Ta sieć wirtualna będzie używana do testowania, w jaki sposób zmiany są raportowane przez co to jest.

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

Po zakończeniu wdrażania możesz sprawdzić, czy operacja co należy wykonać. Tym razem wdrażasz [szablon, który zmienia sieć wirtualną](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Brak jednego z oryginalnych tagów, podsieć została usunięta, a prefiks adresu został zmieniony.

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

Dane wyjściowe, które są wyświetlane podobnie:

![Wdrożenie szablonu Menedżer zasobów dane wyjściowe operacji](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Tekst wyjściowy to:

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

Zwróć uwagę na początku, że kolory są zdefiniowane, aby wskazać typ zmian.

W dolnej części danych wyjściowych widać, że właściciel tagu został usunięty. Prefiks adresu został zmieniony z 10.0.0.0/16 na 10.0.0.0/15. Podsieć o nazwie subnet001 została usunięta. Pamiętaj, że te zmiany nie zostały wdrożone. Zobaczysz Podgląd zmian, które nastąpią w przypadku wdrożenia szablonu.

Niektóre z właściwości, które są wymienione jako usunięte, nie ulegną zmianie. Właściwości mogą być nieprawidłowo zgłaszane jako usunięte, jeśli nie znajdują się w szablonie, ale są automatycznie ustawiane podczas wdrażania jako wartości domyślne. Ten wynik jest uznawany za "szum" w odpowiedzi "co jeśli". Ostatecznie wdrożony zasób będzie miał wartości ustawione dla właściwości. Zgodnie z oczekiwaniami, te właściwości zostaną odfiltrowane z wyniku.

## <a name="programmatically-evaluate-what-if-results"></a>Programowo Oceń wyniki

Teraz można programowo oszacować wyniki działania warunkowego, ustawiając polecenie na zmienną.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Można zobaczyć podsumowanie każdej zmiany.

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

Operacja działania warunkowego obsługuje używanie [trybu wdrożenia](deployment-modes.md). Po ustawieniu na tryb kompletny zasoby, które nie znajdują się w szablonie, są usuwane. W poniższym przykładzie wdrożono [szablon, który nie zawiera żadnych zasobów zdefiniowanych](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) w trybie kompletnym.

Aby wyświetlić podgląd zmian przed wdrożeniem szablonu, użyj polecenia Confirm Switch z poleceniem Deployment. Jeśli zmiany są zgodnie z oczekiwaniami, potwierdź, że wdrożenie ma zostać ukończone.

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

Ponieważ żadne zasoby nie są zdefiniowane w szablonie, a Tryb wdrożenia jest ustawiony na ukończono, Sieć wirtualna zostanie usunięta.

![Wdrożenie szablonu Menedżer zasobów wykonywanie trybu wdrożenia danych wyjściowych operacji](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Tekst wyjściowy to:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Zobaczysz oczekiwane zmiany i można potwierdzić, że chcesz, aby wdrożenie zostało uruchomione.

## <a name="sdks"></a>Zestawy SDK

Operacji działania warunkowego można użyć w ramach zestawów SDK platformy Azure.

* W przypadku języka Python Użyj elementu [co](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations?view=azure-python#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-).

* W przypadku języka Java należy użyć [klasy DeploymentWhatIf](/java/api/com.microsoft.azure.management.resources.deploymentwhatif?view=azure-java-stable).

* W przypadku platformy .NET Użyj [klasy DeploymentWhatIf](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif?view=azure-dotnet).

## <a name="next-steps"></a>Następne kroki

- Jeśli zauważysz nieprawidłowe wyniki z wersji zapoznawczej rozwiązania tego problemu, zgłoś problemy pod adresem [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .
- Aby wdrożyć szablony z Azure PowerShell, zobacz [wdrażanie zasobów przy użyciu szablonów ARM i Azure PowerShell](deploy-powershell.md).
- Aby wdrożyć szablony przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [wdrażanie zasobów za pomocą szablonów ARM i interfejsu wiersza polecenia platformy Azure](deploy-cli.md).
- Aby wdrożyć szablony przy użyciu usługi REST, zobacz [wdrażanie zasobów za pomocą szablonów ARM i interfejs API REST Menedżer zasobów](deploy-rest.md).
