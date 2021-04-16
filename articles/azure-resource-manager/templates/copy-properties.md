---
title: Definiowanie wielu wystąpień właściwości
description: Użyj operacji kopiowania w Azure Resource Manager szablonu usługi Arm, aby wielokrotnie iterować podczas tworzenia właściwości w zasobie.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 16c293f1c3aff64aeb8b6cae4b7f1aa14dcd0a77
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480005"
---
# <a name="property-iteration-in-arm-templates"></a>Iteracja właściwości w szablonach arm

W tym artykule pokazano, jak utworzyć więcej niż jedno wystąpienie właściwości w szablonie usługi Azure Resource Manager (szablon usługi ARM). Dodając pętlę kopiowania do sekcji właściwości zasobu w szablonie, można dynamicznie ustawić liczbę elementów dla właściwości podczas wdrażania. Można również uniknąć konieczności powtarzania składni szablonu.

Pętli kopiowania można używać tylko z zasobami najwyższego poziomu, nawet w przypadku stosowania pętli kopiowania do właściwości. Aby dowiedzieć się więcej o zmianie zasobu podrzędnego na zasób najwyższego poziomu, zobacz [Iteracja zasobu podrzędnego](copy-resources.md#iteration-for-a-child-resource).

Możesz również użyć pętli kopiowania z [zasobami](copy-resources.md), [zmiennymi](copy-variables.md)i [wyjściami](copy-outputs.md).

## <a name="syntax"></a>Składnia

# <a name="json"></a>[JSON](#tab/json)

Dodaj element do sekcji resources szablonu, aby ustawić `copy` liczbę elementów dla właściwości. Element copy ma następujący ogólny format:

```json
"copy": [
  {
    "name": "<name-of-property>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

W `name` przypadku pliku podaj nazwę właściwości zasobu, którą chcesz utworzyć.

Właściwość `count` określa liczbę iteracji, które mają być dla właściwości.

Właściwość `input` określa właściwości, które chcesz powtórzyć. Tworzysz tablicę elementów zbudowaną na wartości we właściwości `input` .

# <a name="bicep"></a>[Bicep](#tab/bicep)

Pętle mogą być używane do deklarowania wielu właściwości przez:

- Iterowanie po tablicy:

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

- Iterowanie po elementach tablicy

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

- Używanie indeksu pętli

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

---

## <a name="copy-limits"></a>Limity kopiowania

Ta liczba nie może przekraczać 800.

Liczba nie może być liczbą ujemną. W przypadku wdrażania szablonu przy użyciu najnowszej wersji interfejsu wiersza polecenia platformy Azure, programu PowerShell lub interfejsu API REST może być zero. W szczególności należy użyć:

- Azure PowerShell **2.6** lub nowszy
- Interfejs wiersza polecenia platformy Azure **w wersji 2.0.74** lub nowszej
- Interfejs API REST **w wersji 2019-05-10** lub nowszej
- [Połączone wdrożenia muszą](linked-templates.md) używać interfejsu API w wersji **2019-05-10** lub nowszej dla typu zasobu wdrożenia

Wcześniejsze wersje programu PowerShell, interfejsu wiersza polecenia i interfejsu API REST nie obsługują zera dla liczby.

## <a name="property-iteration"></a>Iteracja właściwości

W poniższym przykładzie pokazano, jak zastosować pętlę kopiowania do `dataDisks` właściwości na maszynie wirtualnej:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 3,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty",
                "diskSizeGB": 1023
              }
            }
          ]
        }
        ...
      }
    }
  ]
}
```

Należy zauważyć, że w przypadku używania właściwości wewnątrz iteracji należy `copyIndex` podać nazwę iteracji. Iteracja właściwości obsługuje również argument przesunięcia. Przesunięcie musi znajdować się po nazwie iteracji, takiej jak `copyIndex('dataDisks', 1)` .

Wdrożony szablon stanie się:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

Operacja kopiowania jest przydatna podczas pracy z tablicami, ponieważ można iterować po poszczególnych elementach tablicy. Użyj funkcji w tablicy , aby określić liczbę iteracji i pobrać `length` `copyIndex` bieżący indeks w tablicy.

Poniższy przykładowy szablon tworzy grupę trybu failover dla baz danych, które są przekazywane jako tablica.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "primaryServerName": {
      "type": "string"
    },
    "secondaryServerName": {
      "type": "string"
    },
    "databaseNames": {
      "type": "array",
      "defaultValue": [
        "mydb1",
        "mydb2",
        "mydb3"
      ]
    }
  },
  "variables": {
    "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/failoverGroups",
      "apiVersion": "2015-05-01-preview",
      "name": "[variables('failoverName')]",
      "properties": {
        "readWriteEndpoint": {
          "failoverPolicy": "Automatic",
          "failoverWithDataLossGracePeriodMinutes": 60
        },
        "readOnlyEndpoint": {
          "failoverPolicy": "Disabled"
        },
        "partnerServers": [
          {
            "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
          }
        ],
        "copy": [
          {
            "name": "databases",
            "count": "[length(parameters('databaseNames'))]",
            "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
          }
        ]
      }
    }
  ],
  "outputs": {
  }
}
```

Element `copy` jest tablicą, więc można określić więcej niż jedną właściwość dla zasobu.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(0)
@maxValue(16)
@description('The number of dataDisks to be returned in the output array.')
param numberOfDataDisks int = 16

resource vmName 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
  properties: {
    storageProfile: {
      ...
      dataDisks: [for i in range(0, numberOfDataDisks): {
        lun: i
        createOption: 'Empty'
        diskSizeGB: 1023
      }]
    }
    ...
  }
}
```

Wdrożony szablon stanie się:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

---

Iteracji zasobów i właściwości można używać razem. Odwołaj się do iteracji właściwości według nazwy.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource vnetname_resource 'Microsoft.Network/virtualNetworks@2018-04-01' = [for i in range(0, 2): {
  name: concat(vnetname, i)
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        addressPrefix
      ]
    }
    subnets: [for j in range(0, 2): {
      name: 'subnet-${j}'
      properties: {
        addressPrefix: subnetAddressPrefix[j]
      }
    }]
  }
}]
```

---

## <a name="example-templates"></a>Przykładowe szablony

W poniższym przykładzie pokazano wspólny scenariusz tworzenia więcej niż jednej wartości dla właściwości.

|Template  |Opis  |
|---------|---------|
|[Wdrażanie maszyny wirtualnej ze zmienną liczbą dysków danych](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Wdraża kilka dysków danych za pomocą maszyny wirtualnej. |

## <a name="next-steps"></a>Następne kroki

- Aby przejść przez samouczek, zobacz [Samouczek: tworzenie wielu wystąpień zasobów za pomocą szablonów usługi ARM.](template-tutorial-create-multiple-instances.md)
- W przypadku innych zastosowań pętli kopiowania zobacz:
  - [Iteracja zasobów w szablonach arm](copy-resources.md)
  - [Iteracja zmiennej w szablonach arm](copy-variables.md)
  - [Iteracja danych wyjściowych w szablonach arm](copy-outputs.md)
- Jeśli chcesz dowiedzieć się więcej o sekcjach szablonu, zobacz Understand the structure and syntax of ARM templates (Opis struktury [i składni szablonów usługi ARM).](template-syntax.md)
- Aby dowiedzieć się, jak wdrożyć szablon, zobacz [Deploy resources with ARM templates and Azure PowerShell](deploy-powershell.md)(Wdrażanie zasobów za pomocą szablonów usługi ARM i Azure PowerShell ).
