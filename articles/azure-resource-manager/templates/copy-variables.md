---
title: Definiowanie wielu wystąpień zmiennej
description: Użyj operacji kopiowania w szablonie Azure Resource Manager (szablon ARM) do wielokrotnego iteracji podczas tworzenia zmiennej.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: e1f6dfeb7b701b09ad1a9505d5dbcfddf2cd6b0b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385715"
---
# <a name="variable-iteration-in-arm-templates"></a>Iteracja zmiennej w szablonach ARM

W tym artykule pokazano, jak utworzyć więcej niż jedną wartość zmiennej w szablonie Azure Resource Manager (szablon ARM). Poprzez dodanie `copy` elementu do sekcji zmienne szablonu, można dynamicznie ustawić liczbę elementów dla zmiennej podczas wdrażania. Należy również unikać powtarzania składni szablonu.

Można również użyć kopiowania z [zasobami](copy-resources.md), [właściwości w zasobów](copy-properties.md)i danych [wyjściowych](copy-outputs.md).

## <a name="syntax"></a>Składnia

Element Copy ma następujący format ogólny:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

`name`Właściwość jest dowolną wartością, która identyfikuje pętlę. `count`Właściwość określa liczbę iteracji dla zmiennej.

`input`Właściwość określa właściwości, które chcesz powtórzyć. Tworzysz tablicę elementów skonstruowanych na podstawie wartości we `input` właściwości. Może to być pojedyncza Właściwość (na przykład ciąg) lub obiekt z kilkoma właściwościami.

## <a name="copy-limits"></a>Limity kopiowania

Liczba nie może przekraczać 800.

Liczba nie może być liczbą ujemną. Może to być zero, jeśli szablon zostanie wdrożony przy użyciu najnowszej wersji interfejsu wiersza polecenia platformy Azure, programu PowerShell lub API REST. W tym celu należy użyć:

- Azure PowerShell **2,6** lub nowszy
- Interfejs wiersza polecenia platformy Azure **2.0.74** lub nowszy
- Interfejs API REST w wersji **2019-05-10** lub nowszej
- [Połączone wdrożenia](linked-templates.md) muszą używać interfejsu API w wersji **2019-05-10** lub nowszej dla typu zasobu wdrożenia

We wcześniejszych wersjach programu PowerShell, interfejsu wiersza polecenia i interfejsie API REST nie są obsługiwane wartości zerowe.

## <a name="variable-iteration"></a>Iteracja zmiennej

Poniższy przykład pokazuje, jak utworzyć tablicę wartości ciągów:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "arrayResult": {
      "type": "array",
      "value": "[variables('stringArray')]"
    }
  }
}
```

Poprzedni szablon zwraca tablicę z następującymi wartościami:

```json
[
  "item1",
  "item2",
  "item3",
  "item4",
  "item5"
]
```

W następnym przykładzie pokazano, jak utworzyć tablicę obiektów z trzema właściwościami — `name` , `diskSizeGB` i `diskIndex` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "objectArray",
        "count": "[parameters('itemCount')]",
        "input": {
          "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('objectArray')]"
        }
      }
    ]
  },
  "resources": [],
  "outputs": {
    "arrayResult": {
      "type": "array",
      "value": "[variables('objectArray')]"
    }
  }
}
```

Poprzedni przykład zwraca tablicę o następujących wartościach:

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

> [!NOTE]
> Zmienna Variable obsługuje argument przesunięcia. Przesunięcie musi następować po nazwie iteracji, takiej jak `copyIndex('diskNames', 1)` . Jeśli nie podano wartości przesunięcia, domyślnie zostanie ustawiona wartość 0 dla pierwszego wystąpienia.
>

Można również użyć `copy` elementu wewnątrz zmiennej. Poniższy przykład tworzy obiekt, który ma tablicę jako jedną z jej wartości.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "topLevelObject": {
      "sampleProperty": "sampleValue",
      "copy": [
        {
          "name": "disks",
          "count": "[parameters('itemCount')]",
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        }
      ]
    }
  },
  "resources": [],
  "outputs": {
    "objectResult": {
      "type": "object",
      "value": "[variables('topLevelObject')]"
    }
  }
}
```

Poprzedni przykład zwraca obiekt o następujących wartościach:

```json
{
  "sampleProperty": "sampleValue",
  "disks": [
    {
      "name": "myDataDisk1",
      "diskSizeGB": "1",
      "diskIndex": 0
    },
    {
      "name": "myDataDisk2",
      "diskSizeGB": "1",
      "diskIndex": 1
    },
    {
      "name": "myDataDisk3",
      "diskSizeGB": "1",
      "diskIndex": 2
    },
    {
      "name": "myDataDisk4",
      "diskSizeGB": "1",
      "diskIndex": 3
    },
    {
      "name": "myDataDisk5",
      "diskSizeGB": "1",
      "diskIndex": 4
    }
  ]
}
```

W następnym przykładzie pokazano różne sposoby używania `copy` zmiennych.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="example-templates"></a>Przykładowe szablony

W poniższych przykładach przedstawiono typowe scenariusze tworzenia więcej niż jednej wartości dla zmiennej.

|Template  |Opis  |
|---------|---------|
|[Kopiuj zmienne](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Ilustruje różne sposoby iteracji na zmiennych. |
|[Wiele reguł zabezpieczeń](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Wdraża kilka reguł zabezpieczeń w sieciowej grupie zabezpieczeń. Konstruuje reguły zabezpieczeń z parametru. Dla parametru zobacz [wiele plików parametrów sieciowej grupy zabezpieczeń](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Następne kroki

- Aby przejść przez samouczek, zobacz [Samouczek: Tworzenie wielu wystąpień zasobów przy użyciu szablonów ARM](template-tutorial-create-multiple-instances.md).
- Aby poznać inne zastosowania elementu Copy, zobacz:
  - [Iteracja zasobów w szablonach ARM](copy-resources.md)
  - [Iteracja właściwości w szablonach ARM](copy-properties.md)
  - [Iteracja danych wyjściowych w szablonach ARM](copy-outputs.md)
- Jeśli chcesz dowiedzieć się więcej na temat sekcji szablonu, zobacz [Omówienie struktury i składni szablonów ARM](template-syntax.md).
- Aby dowiedzieć się, jak wdrożyć szablon, zobacz [wdrażanie zasobów przy użyciu szablonów ARM i Azure PowerShell](deploy-powershell.md).
