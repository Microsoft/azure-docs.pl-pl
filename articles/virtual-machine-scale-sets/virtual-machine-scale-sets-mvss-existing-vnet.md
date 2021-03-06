---
title: Odwoływanie się do istniejącej sieci wirtualnej w szablonie zestawu skalowania platformy Azure
description: Dowiedz się, jak dodać sieć wirtualną do istniejącego szablonu zestawu skalowania maszyn wirtualnych platformy Azure
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 03/30/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f15fddc54f4b7c5a03843da1bcc11d1991b70d02
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076670"
---
# <a name="reference-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Odwoływanie się do istniejącej sieci wirtualnej w szablonie zestawu skalowania platformy Azure

W tym artykule pokazano, jak zmodyfikować [podstawowy szablon zestawu skalowania](virtual-machine-scale-sets-mvss-start.md) w celu wdrożenia go w istniejącej sieci wirtualnej, zamiast tworzyć nowe.

## <a name="prerequisites"></a>Wymagania wstępne

W poprzednim artykule został utworzony [podstawowy szablon zestawu skalowania](virtual-machine-scale-sets-mvss-start.md). Potrzebny będzie ten wcześniejszy szablon, aby można było go zmodyfikować, aby utworzyć szablon, który wdraża zestaw skalowania w istniejącej sieci wirtualnej.

## <a name="identify-subnet"></a>Zidentyfikuj podsieć

Najpierw Dodaj `subnetId` parametr. Ten ciąg jest przesyłany do konfiguracji zestawu skalowania, co pozwala zestawowi skalowania identyfikować wstępnie utworzoną podsieć, w której mają zostać wdrożone maszyny wirtualne. Ten ciąg musi mieć postać:

`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`


Na przykład w celu wdrożenia zestawu skalowania w istniejącej sieci wirtualnej z nazwą `myvnet` , podsiecią `mysubnet` , grupą zasobów `myrg` i subskrypcją `00000000-0000-0000-0000-000000000000` subnetId będzie: 

`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
      },
      "adminPassword": {
        "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
      }
    },
```

## <a name="delete-extra-virtual-network-resource"></a>Usuń dodatkowy zasób sieci wirtualnej

Następnie usuń zasób sieci wirtualnej z `resources` tablicy, gdy korzystasz z istniejącej sieci wirtualnej i nie musisz wdrażać nowego.

```diff
    "variables": {},
    "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```
## <a name="remove-dependency-clause"></a>Usuń klauzulę zależności

Sieć wirtualna już istnieje przed wdrożeniem szablonu, dlatego nie ma potrzeby określania `dependsOn` klauzuli z zestawu skalowania do sieci wirtualnej. Usuń następujące wiersze:

```diff
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets",
        "name": "myScaleSet",
        "location": "[resourceGroup().location]",
        "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
        "sku": {
          "name": "Standard_A1",
          "capacity": 2
```

## <a name="pass-subnet-parameter"></a>Parametr przekazywania podsieci

Na koniec należy przekazać `subnetId` parametr ustawiony przez użytkownika (zamiast używać polecenia `resourceId` , aby uzyskać identyfikator sieci wirtualnej w tym samym wdrożeniu, co to jest podstawowy, żywotny zestaw skalowania).

```diff
                        "name": "myIpConfig",
                        "properties": {
                          "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                          }
                        }
                      }
```


## <a name="next-steps"></a>Następne kroki

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
