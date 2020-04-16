---
title: Definiowanie wielu wystąpień właściwości
description: Użyj operacji kopiowania w szablonie usługi Azure Resource Manager, aby iterować wiele razy podczas tworzenia właściwości na zasobie.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 831ae1af202a1cdf52bdd2bdf0d9a042a97ba52f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391337"
---
# <a name="property-iteration-in-arm-templates"></a>Iteracja właściwości w szablonach ARM

W tym artykule pokazano, jak utworzyć więcej niż jedno wystąpienie właściwości w szablonie usługi Azure Resource Manager (ARM). Dodając element **kopiowania** do sekcji właściwości zasobu w szablonie, można dynamicznie ustawić liczbę elementów dla właściwości podczas wdrażania. Można również unikać konieczności powtarzania składni szablonu.

Można również użyć kopiowania z [zasobami, zmiennymi](copy-variables.md)i [wyjściowymi](copy-outputs.md). [resources](copy-resources.md)

## <a name="property-iteration"></a>Iteracja właściwości

Element kopiowania ma następujący ogólny format:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Dla **name**, podaj nazwę właściwości zasobu, który chcesz utworzyć.

Właściwość **count** określa liczbę iteracji, które mają dla właściwości.

Właściwość **input** określa właściwości, które mają zostać powtórzone. Można utworzyć tablicę elementów zbudowanych na podstawie wartości we właściwości **wejściowej.**

W poniższym przykładzie `copy` pokazano, jak zastosować do dataDisks właściwości na maszynie wirtualnej:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

Należy zauważyć, `copyIndex` że podczas korzystania z iteracji właściwości, należy podać nazwę iteracji. Iteracja właściwości obsługuje również argument przesunięcia. Przesunięcie musi pochodzić po nazwie iteracji, takich jak copyIndex('dataDisks', 1).

Menedżer zasobów rozszerza `copy` tablicę podczas wdrażania. Nazwa tablicy staje się nazwą właściwości. Wartości wejściowe stają się właściwościami obiektu. Wdrożony szablon staje się:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
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

Operacja kopiowania jest przydatna podczas pracy z tablicami, ponieważ można iterować za pośrednictwem każdego elementu w tablicy. Użyj `length` funkcji w tablicy, aby określić liczbę `copyIndex` iteracji i pobrać bieżący indeks w tablicy.

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

Element kopiowania jest tablicą, dzięki czemu można określić więcej niż jedną właściwość dla zasobu.

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

Można użyć iteracji zasobów i właściwości razem. Odwołanie się do iteracji właściwości według nazwy.

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

## <a name="copy-limits"></a>Limity kopiowania

Liczba nie może przekroczyć 800.

Liczba nie może być liczbą ujemną. Jeśli wdrożysz szablon z programem Azure PowerShell 2.6 lub nowszym, interfejsem wiersza polecenia platformy Azure 2.0.74 lub nowszym lub interfejsem API REST w wersji **2019-05-10** lub nowszej, można ustawić liczbę na zero. Starsze wersje programu PowerShell, interfejsu wiersza polecenia i interfejsu API REST nie obsługują zero dla liczby.

## <a name="example-templates"></a>Przykładowe szablony

W poniższym przykładzie przedstawiono typowy scenariusz tworzenia więcej niż jednej wartości dla właściwości.

|Szablon  |Opis  |
|---------|---------|
|[Wdrażanie maszyn wirtualnych ze zmienną liczbą dysków z danymi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Wdraża kilka dysków danych z maszyną wirtualną. |

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: tworzenie wielu wystąpień zasobów przy użyciu szablonów ARM](template-tutorial-create-multiple-instances.md).
* Aby uzyskać inne zastosowania elementu kopiowania, zobacz:
  * [Iteracja zasobów w szablonach ARM](copy-resources.md)
  * [Iteracja zmiennych w szablonach ARM](copy-variables.md)
  * [Iteracja wyjściowa w szablonach ARM](copy-outputs.md)
* Jeśli chcesz dowiedzieć się więcej o sekcjach szablonu, zobacz [Tworzenie szablonów ARM](template-syntax.md).
* Aby dowiedzieć się, jak wdrożyć szablon, zobacz [Wdrażanie aplikacji z szablonem ARM](deploy-powershell.md).

