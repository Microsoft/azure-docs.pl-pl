---
title: Wymuszanie zabezpieczeń za pomocą zasad na maszynach wirtualnych z systemem Linux na platformie Azure
description: Jak zastosować zasadę do maszyny wirtualnej usługi Azure Resource Manager z systemem Linux
author: mimckitt
ms.service: virtual-machines-linux
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 6eb571ccc8c996a06d3bdf2dda25860df4dc930f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759365"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Stosowanie zasad do maszyn wirtualnych z systemem Linux za pomocą usługi Azure Resource Manager
Korzystając z zasad, organizacja może wymuszać różne konwencje i reguły w całym przedsiębiorstwie. Wymuszanie pożądanego zachowania może pomóc zmniejszyć ryzyko, przyczyniając się jednocześnie do sukcesu organizacji. W tym artykule opisano, jak można użyć zasad usługi Azure Resource Manager do definiowania żądanego zachowania dla maszyn wirtualnych organizacji.

Aby zapoznać się z wprowadzeniem do zasad, zobacz [Co to jest zasady platformy Azure?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Dozwolone maszyny wirtualne
Aby upewnić się, że maszyny wirtualne dla twojej organizacji są zgodne z aplikacją, można ograniczyć dozwolone systemy operacyjne. W poniższym przykładzie zasad zezwalasz tylko na tworzenie maszyn wirtualnych Ubuntu 14.04.2-LTS.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Użyj dzikiej karty, aby zmodyfikować poprzednie zasady, aby zezwolić na dowolny obraz Ubuntu LTS: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Aby uzyskać informacje o polach zasad, zobacz [Aliasy zasad](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Dyski zarządzane

Aby wymagać użycia dysków zarządzanych, należy użyć następujących zasad:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Obrazy dla maszyn wirtualnych

Ze względów bezpieczeństwa można wymagać, aby tylko zatwierdzone obrazy niestandardowe były wdrażane w twoim środowisku. Można określić grupę zasobów zawierającą zatwierdzone obrazy lub określone zatwierdzone obrazy.

Poniższy przykład wymaga obrazów z zatwierdzonej grupy zasobów:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

Poniższy przykład określa zatwierdzone identyfikatory obrazów:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Rozszerzenia maszyny wirtualnej

Można zabronić używania niektórych typów rozszerzeń. Na przykład rozszerzenie może nie być zgodne z niektórymi niestandardowymi obrazami maszyn wirtualnych. W poniższym przykładzie pokazano, jak zablokować określone rozszerzenie. Używa wydawcy i typu, aby określić, które rozszerzenie do zablokowania.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>Następne kroki
* Po zdefiniowaniu reguły zasad (jak pokazano w poprzednich przykładach), należy utworzyć definicję zasad i przypisać ją do zakresu. Zakres może być subskrypcją, grupą zasobów lub zasobem. Aby przypisać zasady, zobacz [Przypisywanie zasad zasobów za pomocą witryny Azure Portal oraz zarządzanie nimi](../../governance/policy/assign-policy-portal.md), [Przypisywanie zasad za pomocą programu PowerShell](../../governance/policy/assign-policy-powershell.md)lub przypisywanie zasad za pomocą interfejsu [wiersza polecenia platformy Azure.](../../governance/policy/assign-policy-azurecli.md)
* Aby zapoznać się z wprowadzeniem do zasad dotyczących zasobów, zobacz [Co to jest zasady platformy Azure?](../../governance/policy/overview.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).
