---
title: Definiowanie wielu wystąpień wartości wyjściowej
description: Użyj operacji kopiowania w szablonie usługi Azure Resource Manager, aby iterować wiele razy podczas zwracania wartości z wdrożenia.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 0315af2f083285c4704b08fec608341b6f0b2231
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617836"
---
# <a name="output-iteration-in-arm-templates"></a>Iteracja wyjściowa w szablonach ARM

W tym artykule pokazano, jak utworzyć więcej niż jedną wartość dla danych wyjściowych w szablonie usługi Azure Resource Manager (ARM). Dodając element **kopiowania** do sekcji dane wyjściowe szablonu, można dynamicznie zwracać liczbę elementów podczas wdrażania.

Można również użyć kopiowania z [resources](copy-resources.md) [zasobami, właściwości w zasobie](copy-properties.md)i [zmiennych](copy-variables.md).

## <a name="outputs-iteration"></a>Wyprowadza iterację

Element kopiowania ma następujący ogólny format:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

Właściwość **count** określa liczbę iteracji, które mają dla wartości wyjściowej.

Właściwość **input** określa właściwości, które mają zostać powtórzone. Można utworzyć tablicę elementów zbudowanych na podstawie wartości we właściwości **wejściowej.** Może to być pojedyncza właściwość (jak ciąg) lub obiekt z kilkoma właściwościami.

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

W poprzednim przykładzie zwraca tablicę z następującymi wartościami:

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

* Aby przejść przez samouczek, zobacz [Samouczek: tworzenie wielu wystąpień zasobów przy użyciu szablonów ARM](template-tutorial-create-multiple-instances.md).
* Aby uzyskać inne zastosowania elementu kopiowania, zobacz:
  * [Iteracja zasobów w szablonach ARM](copy-resources.md)
  * [Iteracja właściwości w szablonach ARM](copy-properties.md)
  * [Iteracja zmiennych w szablonach ARM](copy-variables.md)
* Jeśli chcesz dowiedzieć się więcej o sekcjach szablonu, zobacz [Tworzenie szablonów ARM](template-syntax.md).
* Aby dowiedzieć się, jak wdrożyć szablon, zobacz [Wdrażanie aplikacji z szablonem ARM](deploy-powershell.md).

