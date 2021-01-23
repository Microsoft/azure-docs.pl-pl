---
title: Tworzenie i wdrażanie specyfikacji szablonu
description: Opisuje sposób tworzenia specyfikacji szablonu i udostępniania ich innym użytkownikom w organizacji.
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 762c483883d391c436065b13b54f127f1618d7f9
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734919"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Specyfikacje szablonu Azure Resource Manager (wersja zapoznawcza)

Specyfikacja szablonu to typ zasobu do przechowywania szablonu Azure Resource Manager (szablon ARM) na platformie Azure na potrzeby późniejszego wdrożenia. Ten typ zasobu umożliwia udostępnianie szablonów ARM innym użytkownikom w organizacji. Podobnie jak w przypadku dowolnego innego zasobu platformy Azure, możesz użyć kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby udostępnić specyfikację szablonu.

**Microsoft. resources/templateSpecs** to typ zasobu dla specyfikacji szablonu. Składa się z szablonu głównego i dowolnej liczby połączonych szablonów. Platforma Azure bezpiecznie przechowuje specyfikacje szablonu w grupach zasobów. Specyfikacje szablonu obsługują [przechowywanie wersji](#versioning).

Do wdrożenia specyfikacji szablonu można używać standardowych narzędzi platformy Azure, takich jak PowerShell, interfejs wiersza polecenia platformy Azure, Azure Portal, REST i inne obsługiwane zestawy SDK i klienci. Używasz tych samych poleceń, co w przypadku szablonu.

> [!NOTE]
> Specyfikacje szablonu są obecnie w wersji zapoznawczej. Aby można było używać go z Azure PowerShell, należy zainstalować [wersję 5.0.0 lub nowszą](/powershell/azure/install-az-ps). Aby użyć go z interfejsem wiersza polecenia platformy Azure, użyj [wersji 2.14.2 lub nowszej](/cli/azure/install-azure-cli).

## <a name="why-use-template-specs"></a>Dlaczego warto używać specyfikacji szablonu?

Jeśli masz obecnie szablony w repozytorium GitHub lub koncie magazynu, będziesz mieć kilka wyzwań podczas próby udostępnienia szablonów i używania ich. Aby użytkownik mógł wdrożyć ten szablon, musi być on lokalny lub adres URL szablonu musi być dostępny publicznie. Aby obejść to ograniczenie, można udostępnić kopie szablonu użytkownikom, którzy muszą je wdrożyć, lub otworzyć dostęp do repozytorium lub konta magazynu. Gdy użytkownicy są właścicielami lokalnych szablonów, te kopie mogą ostatecznie zależeć od oryginalnego szablonu. W przypadku udostępnienia publicznie repozytorium lub konta magazynu możesz zezwolić niektórym użytkownikom na dostęp do tego szablonu.

Zaletą korzystania z specyfikacji szablonów jest możliwość tworzenia szablonów kanonicznych i udostępniania ich zespołom w organizacji. Specyfikacje szablonu są bezpieczne, ponieważ są dostępne do Azure Resource Manager do wdrożenia, ale nie są dostępne dla użytkowników bez uprawnień Azure RBAC. Użytkownicy potrzebują tylko dostępu do odczytu specyfikacji szablonu, aby wdrożyć jej szablon, dzięki czemu można udostępnić szablon bez zezwalania innym osobom na jego Modyfikowanie.

Szablony zawarte w specyfikacji szablonu powinny być weryfikowane przez administratorów w organizacji, aby przestrzegać wymagań i wskazówek dotyczących organizacji.

## <a name="create-template-spec"></a>Utwórz specyfikację szablonu

W poniższym przykładzie przedstawiono prosty szablon służący do tworzenia konta magazynu na platformie Azure.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

Podczas tworzenia specyfikacji szablonu polecenie programu PowerShell lub interfejsu wiersza polecenia są przesyłane do głównego pliku szablonu. Jeśli szablon główny odwołuje się do połączonych szablonów, polecenia znajdą i pakują je w celu utworzenia specyfikacji szablonu. Aby dowiedzieć się więcej, zobacz [Tworzenie specyfikacji szablonu z połączonymi szablonami](#create-a-template-spec-with-linked-templates).

Utwórz specyfikację szablonu przy użyciu:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0a -ResourceGroupName templateSpecsRg -Location westus2 -TemplateFile ./mainTemplate.json
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

Wszystkie specyfikacje szablonu można wyświetlić w ramach subskrypcji przy użyciu:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli
az ts list
```

---

Możesz wyświetlić szczegóły specyfikacji szablonu, w tym jej wersje, z:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli
az ts show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0a"
```

---

## <a name="deploy-template-spec"></a>Wdróż specyfikację szablonu

Po utworzeniu specyfikacji szablonu użytkownicy z dostępem do **odczytu** do specyfikacji szablonu mogą ją wdrożyć. Aby uzyskać informacje na temat udzielania dostępu, zobacz [Samouczek: udzielanie grupie dostępu do zasobów platformy Azure przy użyciu Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

Specyfikacje szablonu można wdrożyć za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu połączonego w ramach wdrożenia większego szablonu. Użytkownicy w organizacji mogą wdrożyć specyfikację szablonu w dowolnym zakresie na platformie Azure (grupy zasobów, subskrypcji, grupy zarządzania lub dzierżawcy).

Zamiast przekazywania ścieżki lub identyfikatora URI szablonu, należy wdrożyć specyfikację szablonu, podając jej identyfikator zasobu. Identyfikator zasobu ma następujący format:

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

Należy zauważyć, że identyfikator zasobu zawiera nazwę wersji specyfikacji szablonu.

Przykładowo można wdrożyć specyfikację szablonu za pomocą następującego polecenia.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

W ćwiczeniu zazwyczaj można uruchomić `Get-AzTemplateSpec` lub `az ts show` uzyskać identyfikator specyfikacji szablonu, która ma zostać wdrożona.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0a).Versions.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0a" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

## <a name="parameters"></a>Parametry

Przekazywanie parametrów do specyfikacji szablonu jest dokładnie takie jak przekazywanie parametrów do szablonu ARM. Dodaj wartości parametrów w postaci wbudowanej lub w pliku parametrów.

Aby przekazać parametr w tekście, użyj:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

Aby utworzyć plik parametrów lokalnych, użyj:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

I przekaż ten plik parametru:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

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
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0a')]"
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
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0a')]"
        }
      }
    }
  ],
  "outputs": {}
}
```

Aby uzyskać więcej informacji na temat łączenia specyfikacji szablonu, zobacz [Samouczek: wdrażanie specyfikacji szablonu jako połączonego szablonu](template-specs-deploy-linked-template.md).

## <a name="versioning"></a>Przechowywanie wersji

Podczas tworzenia specyfikacji szablonu należy podać jej nazwę. Podczas iteracji kodu szablonu można zaktualizować istniejącą wersję (w przypadku poprawek) lub opublikować nową wersję. Wersja jest ciągiem tekstowym. Możesz użyć dowolnego systemu obsługi wersji, w tym wersji semantycznej. Użytkownicy specyfikacji szablonu mogą podać nazwę wersji, która ma być używana podczas jej wdrażania.

## <a name="next-steps"></a>Następne kroki

* Aby utworzyć i wdrożyć specyfikację szablonu, zobacz [Szybki Start: Tworzenie i wdrażanie specyfikacji szablonu](quickstart-create-template-specs.md).

* Aby uzyskać więcej informacji na temat łączenia szablonów w specyfikacji szablonu, zobacz [Samouczek: Tworzenie specyfikacji szablonu z połączonymi szablonami](template-specs-create-linked.md).

* Aby uzyskać więcej informacji na temat wdrażania specyfikacji szablonu jako połączonego szablonu, zobacz [Samouczek: wdrażanie specyfikacji szablonu jako połączonego szablonu](template-specs-deploy-linked-template.md).
