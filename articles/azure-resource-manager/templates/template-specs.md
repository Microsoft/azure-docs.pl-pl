---
title: Przegląd specyfikacji szablonu
description: Opisuje sposób tworzenia specyfikacji szablonu i udostępniania ich innym użytkownikom w organizacji.
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 47dcf44b35ad5c0b77dd0b88d683071a7f2f4ecb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097725"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Specyfikacje szablonu Azure Resource Manager (wersja zapoznawcza)

Specyfikacja szablonu to nowy typ zasobu do przechowywania szablonu Azure Resource Manager (szablon ARM) na platformie Azure na potrzeby późniejszego wdrożenia. Ten typ zasobu umożliwia udostępnianie szablonów ARM innym użytkownikom w organizacji. Podobnie jak w przypadku dowolnego innego zasobu platformy Azure, można użyć kontroli dostępu opartej na rolach (RBAC), aby udostępnić specyfikację szablonu. Użytkownicy muszą mieć dostęp do odczytu do specyfikacji szablonu, aby wdrożyć jej szablon, dzięki czemu można udostępnić szablon bez zezwalania innym na jego Modyfikowanie.

**Microsoft. resources/templateSpecs** to nowy typ zasobu dla specyfikacji szablonu. Składa się z szablonu głównego i dowolnej liczby połączonych szablonów. Platforma Azure bezpiecznie przechowuje specyfikacje szablonu w grupach zasobów. Specyfikacje szablonu obsługują [przechowywanie wersji](#versioning).

Do wdrożenia specyfikacji szablonu można używać standardowych narzędzi platformy Azure, takich jak PowerShell, interfejs wiersza polecenia platformy Azure, Azure Portal, REST i inne obsługiwane zestawy SDK i klienci. Użyj tych samych poleceń i przekaż te same parametry dla szablonu.

Zaletą korzystania z specyfikacji szablonu jest to, że zespoły w organizacji nie muszą ponownie tworzyć ani kopiować szablonów dla typowych scenariuszy. Tworzysz szablony kanoniczne i udostępniaj je. Szablony zawarte w specyfikacji szablonu powinny być weryfikowane przez administratorów w organizacji, aby przestrzegać wymagań i wskazówek dotyczących organizacji.

> [!NOTE]
> Specyfikacje szablonu są obecnie w wersji zapoznawczej. Aby go użyć, musisz [zarejestrować się w celu uzyskania listy oczekiwania](https://aka.ms/templateSpecOnboarding).

## <a name="create-template-spec"></a>Utwórz specyfikację szablonu

W poniższym przykładzie przedstawiono prosty szablon służący do tworzenia konta magazynu na platformie Azure.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

Podczas tworzenia specyfikacji szablonu polecenie programu PowerShell lub interfejsu wiersza polecenia są przesyłane do głównego pliku szablonu. Jeśli szablon główny odwołuje się do połączonych szablonów, polecenia znajdą i pakują je w celu utworzenia specyfikacji szablonu. Aby dowiedzieć się więcej, zobacz [Tworzenie specyfikacji szablonu z połączonymi szablonami](#create-a-template-spec-with-linked-templates).

Utwórz specyfikację szablonu przy użyciu:

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -TemplateJsonFile ./mainTemplate.json
```

Wszystkie specyfikacje szablonu można wyświetlić w ramach subskrypcji przy użyciu:

```azurepowershell
Get-AzTemplateSpec
```

Możesz wyświetlić szczegóły specyfikacji szablonu, w tym jej wersje, z:

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

## <a name="deploy-template-spec"></a>Wdróż specyfikację szablonu

Po utworzeniu specyfikacji szablonu użytkownicy z dostępem do **odczytu** do specyfikacji szablonu mogą ją wdrożyć. Aby uzyskać informacje na temat udzielania dostępu, zobacz [Samouczek: udzielanie grupie dostępu do zasobów platformy Azure przy użyciu Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

Specyfikacje szablonu można wdrożyć za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu połączonego w ramach wdrożenia większego szablonu. Użytkownicy w organizacji mogą wdrożyć specyfikację szablonu w dowolnym zakresie na platformie Azure (grupy zasobów, subskrypcji, grupy zarządzania lub dzierżawcy).

Zamiast przekazywania ścieżki lub identyfikatora URI szablonu, należy wdrożyć specyfikację szablonu, podając jej identyfikator zasobu. Identyfikator zasobu ma następujący format:

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

Należy zauważyć, że identyfikator zasobu zawiera numer wersji dla specyfikacji szablonu.

Przykładowo można wdrożyć specyfikację szablonu za pomocą następującego polecenia programu PowerShell.

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

W ćwiczeniu zazwyczaj można uruchomić polecenie, `Get-AzTemplateSpec` Aby uzyskać identyfikator specyfikacji szablonu, którą chcesz wdrożyć.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

## <a name="create-a-template-spec-with-linked-templates"></a>Tworzenie specyfikacji szablonu z połączonymi szablonami

Jeśli główny szablon specyfikacji szablonu odwołuje się do połączonych szablonów, polecenia programu PowerShell i interfejsu CLI mogą automatycznie znajdować i spakować połączone szablony z dysku lokalnego. Nie trzeba ręcznie konfigurować kont magazynu ani repozytoriów, aby hostować specyfikacje szablonu — wszystko to jest samodzielne w szablonie zasobów specyfikacji.

Poniższy przykład składa się z szablonu głównego z dwoma połączonymi szablonami. Przykładem jest tylko fragment szablonu. Zwróć uwagę, że używa właściwości o nazwie `relativePath` do łączenia z innymi szablonami. `apiVersion` `2020-06-01` Dla zasobu wdrożenia należy użyć programu lub nowszego.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "relativePath": "artifacts/webapp.json"
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "relativePath": "artifacts/database.json"
                }
            }
        }
    ],
    "outputs": {}
}

```

Gdy polecenie programu PowerShell lub interfejsu wiersza polecenia tworzenia specyfikacji szablonu jest wykonywane dla poprzedniego przykładu, polecenie odnajduje trzy pliki — szablon główny, szablon aplikacji sieci Web ( `webapp.json` ) i szablon bazy danych ( `database.json` ) — i umieszcza je w specyfikacji szablonu.

Aby uzyskać więcej informacji, zobacz [Samouczek: Tworzenie specyfikacji szablonu z połączonymi szablonami](template-specs-create-linked.md).

## <a name="deploy-template-spec-as-a-linked-template"></a>Wdróż specyfikację szablonu jako połączony szablon

Po utworzeniu specyfikacji szablonu można ją łatwo ponownie wykorzystać z szablonu ARM lub innej specyfikacji szablonu. Możesz połączyć się z specyfikacją szablonu przez dodanie jej identyfikatora zasobu do szablonu. Specyfikacja szablonu połączonego jest wdrażana automatycznie podczas wdrażania szablonu głównego. Takie zachowanie umożliwia tworzenie specyfikacji szablonu modularnego i ponowne używanie ich w razie konieczności.

Można na przykład utworzyć specyfikację szablonu, która wdraża zasoby sieciowe i inną specyfikację szablonu, która wdraża zasoby magazynu. W szablonach ARM można połączyć te dwie specyfikacje szablonu w dowolnym momencie, gdy trzeba skonfigurować zasoby sieciowe lub magazynowe.

Poniższy przykład jest podobny do wcześniejszego przykładu, ale użyto właściwości, `id` Aby połączyć się z specyfikacją szablonu, a nie `relativePath` właściwością do połączenia z szablonem lokalnym. Użyj `2020-06-01` do wersji interfejsu API dla zasobu wdrożenia. W tym przykładzie specyfikacje szablonu znajdują się w grupie zasobów o nazwie **templateSpecsRG**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "networkingDeployment",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0')]"
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "storageDeployment",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
                }
            }
        }
    ],
    "outputs": {}
}
```

Aby uzyskać więcej informacji na temat łączenia specyfikacji szablonu, zobacz [Samouczek: wdrażanie specyfikacji szablonu jako połączonego szablonu](template-specs-deploy-linked-template.md).

## <a name="versioning"></a>Obsługa wersji

Podczas tworzenia specyfikacji szablonu należy podać dla niej numer wersji. Podczas iteracji kodu szablonu można zaktualizować istniejącą wersję (w przypadku poprawek) lub opublikować nową wersję. Wersja jest ciągiem tekstowym. Możesz użyć dowolnego systemu obsługi wersji, w tym wersji semantycznej. Użytkownicy specyfikacji szablonu mogą podać numer wersji, która ma być używana podczas jej wdrażania.

## <a name="next-steps"></a>Następne kroki

* Aby utworzyć i wdrożyć specyfikację szablonu, zobacz [Szybki Start: Tworzenie i wdrażanie specyfikacji szablonu](quickstart-create-template-specs.md).

* Aby uzyskać więcej informacji na temat łączenia szablonów w specyfikacji szablonu, zobacz [Samouczek: Tworzenie specyfikacji szablonu z połączonymi szablonami](template-specs-create-linked.md).

* Aby uzyskać więcej informacji na temat wdrażania specyfikacji szablonu jako połączonego szablonu, zobacz [Samouczek: wdrażanie specyfikacji szablonu jako połączonego szablonu](template-specs-deploy-linked-template.md).
