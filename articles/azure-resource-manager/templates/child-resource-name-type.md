---
title: Zasoby podrzędne w szablonach
description: Opisuje sposób ustawiania nazwy i typu dla zasobów podrzędnych w szablonie Azure Resource Manager.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 3a69829e674925982c618807f49433a033d8c5f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80743833"
---
# <a name="set-name-and-type-for-child-resources"></a>Ustawianie nazwy i typu dla zasobów podrzędnych

Zasoby podrzędne to zasoby, które istnieją tylko w kontekście innego zasobu. Na przykład [rozszerzenie maszyny wirtualnej](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) nie może istnieć bez [maszyny wirtualnej](/azure/templates/microsoft.compute/2019-03-01/virtualmachines). Zasób rozszerzenia jest elementem podrzędnym maszyny wirtualnej.

W szablonie Menedżer zasobów można określić zasób podrzędny w ramach zasobu nadrzędnego lub poza nim. Poniższy przykład pokazuje zasób podrzędny uwzględniony we właściwości Resources zasobu nadrzędnego.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

W następnym przykładzie pokazano zasób podrzędny poza zasobem nadrzędnym. Tego podejścia można użyć, jeśli zasób nadrzędny nie jest wdrożony w tym samym szablonie lub jeśli chcesz użyć [kopii](copy-resources.md) , aby utworzyć więcej niż jeden zasób podrzędny.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

Wartości, które podano dla nazwy i typu zasobu, różnią się w zależności od tego, czy zasób podrzędny jest zdefiniowany w lub poza zasobem nadrzędnym.

## <a name="within-parent-resource"></a>W ramach zasobu nadrzędnego

W przypadku zdefiniowania w ramach nadrzędnego typu zasobu należy sformatować wartości typu i nazwy jako pojedyncze słowo bez ukośników.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Poniższy przykład przedstawia sieć wirtualną i podsieć. Należy zauważyć, że podsieć jest uwzględniona w tablicy zasobów dla sieci wirtualnej. Nazwa jest ustawiona na **Subnet1** , a typ jest ustawiony na **podsieci**. Zasób podrzędny jest oznaczony jako zależny od zasobu nadrzędnego, ponieważ musi istnieć zasób nadrzędny, aby można było wdrożyć zasób podrzędny.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

Pełny typ zasobu to nadal **Microsoft. Network/virtualNetworks/Subnets**. Nie podajesz usługi **Microsoft. Network/virtualNetworks/** , ponieważ jest ona zajmowana z nadrzędnego typu zasobu.

Nazwa zasobu podrzędnego jest ustawiona na **Subnet1** , ale pełna nazwa zawiera nazwę nadrzędną. Nie udostępniasz **VNet1** , ponieważ jest ona założono z zasobu nadrzędnego.

## <a name="outside-parent-resource"></a>Poza zasobem nadrzędnym

Po zdefiniowaniu poza zasobem nadrzędnym można sformatować typ i z ukośnikami, aby uwzględnić typ i nazwę elementu nadrzędnego.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

W poniższym przykładzie pokazano sieć wirtualną i podsieć, które są zdefiniowane na poziomie głównym. Należy zauważyć, że podsieć nie znajduje się w tablicy Resources dla sieci wirtualnej. Nazwa jest ustawiona na **VNet1/Subnet1** , a typ jest ustawiony na **Microsoft. Network/virtualNetworks/Subnets**. Zasób podrzędny jest oznaczony jako zależny od zasobu nadrzędnego, ponieważ musi istnieć zasób nadrzędny, aby można było wdrożyć zasób podrzędny.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat tworzenia szablonów Azure Resource Manager, zobacz Tworzenie [szablonów](template-syntax.md).

* Aby dowiedzieć się więcej o formacie nazwy zasobu podczas odwoływania się do zasobu, zobacz [Funkcja Reference](template-functions-resource.md#reference).
