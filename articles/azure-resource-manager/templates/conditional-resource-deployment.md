---
title: Wdrożenie warunkowe z szablonami
description: Opisuje sposób warunkowego wdrażania zasobu w szablonie Azure Resource Manager (szablon ARM).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 409d258d7dfe3ed186e5cf97cc0dbe6dc149b849
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741178"
---
# <a name="conditional-deployment-in-arm-templates"></a>Wdrażanie warunkowe w szablonach usługi ARM

Czasami trzeba opcjonalnie wdrożyć zasób w pliku szablonu Azure Resource Manager (szablon ARM) lub Bicep. W przypadku szablonów JSON Użyj `condition` elementu, aby określić, czy zasób został wdrożony. W przypadku Bicep Użyj `if` słowa kluczowego, aby określić, czy zasób został wdrożony. Wartość dla warunku jest równa true lub false. Gdy wartość jest równa true, zasób jest tworzony. Gdy wartość jest równa false, zasób nie zostanie utworzony. Wartość może zostać zastosowana tylko do całego zasobu.

> [!NOTE]
> Wdrożenie warunkowe nie jest kaskadowo do [zasobów podrzędnych](child-resource-name-type.md). Aby warunkowo wdrożyć zasób i jego zasoby podrzędne, należy zastosować ten sam warunek dla każdego typu zasobu.

## <a name="deploy-condition"></a>Warunek wdrożenia

Można przekazać wartość parametru, która wskazuje, czy zasób został wdrożony. Poniższy przykład warunkowo wdraża strefę DNS.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployZone": {
      "type": "bool"
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[parameters('deployZone')]",
      "type": "Microsoft.Network/dnsZones",
      "apiVersion": "2018-05-01",
      "name": "myZone",
      "location": "global"
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

---

Aby uzyskać bardziej złożony przykład, zobacz [Azure SQL Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="new-or-existing-resource"></a>Nowy lub istniejący zasób

Przy użyciu wdrożenia warunkowego można utworzyć nowy zasób lub użyć istniejącego. Poniższy przykład pokazuje, jak wdrożyć nowe konto magazynu lub użyć istniejącego konta magazynu.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "newOrExisting": {
      "type": "string",
      "defaultValue": "new",
      "allowedValues": [
        "new",
        "existing"
      ]
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[equals(parameters('newOrExisting'), 'new')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

Gdy parametr `newOrExisting` jest ustawiony na **Nowy**, warunek ma wartość true. Konto magazynu zostało wdrożone. Jednak gdy `newOrExisting` jest ustawiona na wartość **exist**, warunek jest spełniony, a konto magazynu nie jest wdrożone.

Aby zapoznać się z kompletnym przykładowym szablonem, który używa `condition` elementu, zobacz [maszyna wirtualna z nowym lub istniejącym Virtual Network, magazynem i publicznym adresem IP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="runtime-functions"></a>Funkcje środowiska uruchomieniowego

Jeśli używasz funkcji [odwołania](template-functions-resource.md#reference) lub [listy](template-functions-resource.md#list) z zasobem, który jest wdrażany warunkowo, funkcja jest oceniana, nawet jeśli zasób nie został wdrożony. Występuje błąd, jeśli funkcja odwołuje się do zasobu, który nie istnieje.

Użyj funkcji [if](template-functions-logical.md#if) , aby upewnić się, że funkcja jest obliczana tylko pod kątem warunków, gdy zasób jest wdrożony. Zobacz [funkcję if](template-functions-logical.md#if) , aby zapoznać się z przykładowym szablonem, który używa `if` i `reference` ze wstępnie wdrożonym zasobem.

Zasób jest ustawiany [jako zależny od](define-resource-dependency.md) zasobu warunkowego dokładnie tak samo jak w przypadku każdego innego zasobu. Gdy zasób warunkowy nie zostanie wdrożony, Azure Resource Manager automatycznie usuwa go z wymaganych zależności.

## <a name="complete-mode"></a>Tryb kompletny

Jeśli szablon zostanie wdrożony z [trybem kompletnym](deployment-modes.md) , a zasób nie zostanie wdrożony `condition` , ponieważ zwraca wartość false, wynik zależy od używanej wersji interfejsu API REST do wdrożenia szablonu. W przypadku używania wersji wcześniejszej niż 2019-05-10 zasób nie zostanie **usunięty**. W przypadku 2019-05-10 lub nowszych zasób **jest usuwany**. Najnowsze wersje Azure PowerShell i interfejsu wiersza polecenia platformy Azure usuwają zasób, gdy warunek ma wartość false.

## <a name="next-steps"></a>Następne kroki

* W przypadku modułu Microsoft Learn, który obejmuje wdrożenie warunkowe, zobacz [zarządzanie złożonymi wdrożeniami w chmurze za pomocą zaawansowanych funkcji szablonów usługi ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Aby zapoznać się z zaleceniami dotyczącymi tworzenia szablonów, zobacz [najlepsze rozwiązania dotyczące szablonów ARM](template-best-practices.md).
* Aby utworzyć wiele wystąpień zasobu, zobacz [iteracja zasobów w szablonach ARM](copy-resources.md).
