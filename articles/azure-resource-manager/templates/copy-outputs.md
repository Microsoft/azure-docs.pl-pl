---
title: Zdefiniuj wiele wystąpień wartości wyjściowej
description: Użyj operacji kopiowania w szablonie Azure Resource Manager (szablon ARM) do wielokrotnego iteracji podczas zwracania wartości z wdrożenia.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 5ae20ed9ec3fdb3b76adbd370f5ba22f9386d613
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905948"
---
# <a name="output-iteration-in-arm-templates"></a>Iteracja danych wyjściowych w szablonach ARM

W tym artykule pokazano, jak utworzyć więcej niż jedną wartość danych wyjściowych w szablonie Azure Resource Manager (szablon ARM). Poprzez dodanie `copy` elementu do sekcji dane wyjściowe szablonu można dynamicznie zwrócić liczbę elementów podczas wdrażania.

Można również użyć kopiowania z [zasobami](copy-resources.md), [właściwości w zasobie](copy-properties.md)i [zmiennych](copy-variables.md).

## <a name="syntax"></a>Składnia

Element Copy ma następujący format ogólny:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

`count`Właściwość określa liczbę iteracji dla wartości wyjściowej.

`input`Właściwość określa właściwości, które chcesz powtórzyć. Tworzysz tablicę elementów skonstruowanych na podstawie wartości we `input` właściwości. Może to być pojedyncza Właściwość (na przykład ciąg) lub obiekt z kilkoma właściwościami.

## <a name="copy-limits"></a>Limity kopiowania

Liczba nie może przekraczać 800.

Liczba nie może być liczbą ujemną. Może to być zero, jeśli szablon zostanie wdrożony przy użyciu najnowszej wersji interfejsu wiersza polecenia platformy Azure, programu PowerShell lub API REST. W tym celu należy użyć:

* Azure PowerShell **2,6** lub nowszy
* Interfejs wiersza polecenia platformy Azure **2.0.74** lub nowszy
* Interfejs API REST w wersji **2019-05-10** lub nowszej
* [Połączone wdrożenia](linked-templates.md) muszą używać interfejsu API w wersji **2019-05-10** lub nowszej dla typu zasobu wdrożenia

We wcześniejszych wersjach programu PowerShell, interfejsu wiersza polecenia i interfejsie API REST nie są obsługiwane wartości zerowe.

## <a name="outputs-iteration"></a>Iteracja danych wyjściowych

Poniższy przykład tworzy zmienną liczbę kont magazynu i zwraca punkt końcowy dla każdego konta magazynu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

Poprzedni szablon zwraca tablicę z następującymi wartościami:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

Następny przykład zwraca trzy właściwości z nowych kont magazynu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

Poprzedni przykład zwraca tablicę o następujących wartościach:

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: Tworzenie wielu wystąpień zasobów przy użyciu szablonów ARM](template-tutorial-create-multiple-instances.md).
* Aby poznać inne zastosowania elementu Copy, zobacz:
  * [Iteracja zasobów w szablonach ARM](copy-resources.md)
  * [Iteracja właściwości w szablonach ARM](copy-properties.md)
  * [Iteracja zmiennej w szablonach ARM](copy-variables.md)
* Jeśli chcesz dowiedzieć się więcej na temat sekcji szablonu, zobacz [Omówienie struktury i składni szablonów ARM](template-syntax.md).
* Aby dowiedzieć się, jak wdrożyć szablon, zobacz [wdrażanie zasobów przy użyciu szablonów ARM i Azure PowerShell](deploy-powershell.md).
