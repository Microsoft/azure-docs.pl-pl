---
title: Wymuszanie zabezpieczeń za pomocą zasad na maszynach wirtualnych z systemem Windows na platformie Azure
description: Jak zastosować zasadę do maszyny wirtualnej usługi Azure Resource Manager Windows
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 407e5cefe9f7f60c86de6e80133ff1b3f8b9003d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086542"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Stosowanie zasad do maszyn wirtualnych systemu Windows za pomocą usługi Azure Resource Manager
Korzystając z zasad, organizacja może wymuszać różne konwencje i reguły w całym przedsiębiorstwie. Wymuszanie pożądanego zachowania może pomóc zmniejszyć ryzyko, przyczyniając się jednocześnie do sukcesu organizacji. W tym artykule opisano, jak można użyć zasad usługi Azure Resource Manager do definiowania żądanego zachowania dla maszyn wirtualnych organizacji.

Aby zapoznać się z wprowadzeniem do zasad, zobacz [Co to jest zasady platformy Azure?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Dozwolone maszyny wirtualne
Aby upewnić się, że maszyny wirtualne dla twojej organizacji są zgodne z aplikacją, można ograniczyć dozwolone systemy operacyjne. W poniższym przykładzie zasad zezwalasz na tworzenie tylko maszyn wirtualnych centrum danych systemu Windows Server 2012 R2:

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
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
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

Użyj symbolu wieloznacznego, aby zmodyfikować poprzednie zasady, aby zezwolić na dowolny obraz centrum danych systemu Windows Server:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Użyj anyOf, aby zmodyfikować poprzednie zasady, aby zezwolić na dowolny system Windows Server 2012 R2 Datacenter lub wyższy obraz:

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
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


## <a name="azure-hybrid-use-benefit"></a>Korzyść użycia hybrydowego platformy Azure

Jeśli masz licencję lokalną, możesz ją uiścić na maszynach wirtualnych. Jeśli nie masz licencji, należy zabronić tej opcji. Poniższa zasada zabrania używania korzyści z użycia hybrydowego platformy Azure (AHUB):

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
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
