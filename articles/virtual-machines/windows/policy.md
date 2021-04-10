---
title: Wymuś zabezpieczenia przy użyciu zasad na maszynach wirtualnych z systemem Windows na platformie Azure
description: Jak zastosować zasady do Azure Resource Manager maszyny wirtualnej z systemem Windows
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 4a4e54510c4683dc1be9da09b96d6289136a26f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550267"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Stosowanie zasad do maszyn wirtualnych z systemem Windows za pomocą Azure Resource Manager
Korzystając z zasad, organizacja może wymusić różne konwencje i reguły w całym przedsiębiorstwie. Wymuszanie żądanego zachowania może pomóc w ograniczeniu ryzyka, a tym samym sukcesem organizacji. W tym artykule opisano sposób użycia zasad Azure Resource Managerymi w celu zdefiniowania żądanego zachowania Virtual Machines organizacji.

Aby zapoznać się z wprowadzeniem do zasad, zobacz [co to jest Azure Policy?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Dozwolone Virtual Machines
Aby upewnić się, że maszyny wirtualne w organizacji są zgodne z aplikacją, można ograniczyć dozwolone systemy operacyjne. W poniższym przykładzie zasad można utworzyć tylko Virtual Machines systemu Windows Server 2012 R2 Datacenter:

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

Użyj symbolu wieloznacznego, aby zmodyfikować powyższe zasady w celu zezwolenia na dowolny obraz systemu Windows Server Datacenter:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Użyj anyOf, aby zmodyfikować powyższe zasady w taki sposób, aby zezwalały na dowolny obraz systemu Windows Server 2012 R2 Datacenter lub nowszego:

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

Aby uzyskać informacje o polach zasad, zobacz [aliasy zasad](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Dyski zarządzane

Aby wymagać korzystania z dysków zarządzanych, należy użyć następujących zasad:

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

## <a name="images-for-virtual-machines"></a>Obrazy dla Virtual Machines

Ze względów bezpieczeństwa można wymagać, aby w środowisku wdrożono tylko zatwierdzone obrazy niestandardowe. Można określić grupę zasobów zawierającą zatwierdzone obrazy lub określone zatwierdzone obrazy.

W poniższym przykładzie są wymagane obrazy z zatwierdzonej grupy zasobów:

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

W poniższym przykładzie określono zatwierdzone identyfikatory obrazu:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Rozszerzenia maszyny wirtualnej

Możesz chcieć zabronić użycia niektórych typów rozszerzeń. Na przykład rozszerzenie może być niezgodne z niektórymi niestandardowymi obrazami maszyn wirtualnych. Poniższy przykład pokazuje, jak zablokować określone rozszerzenie. Używa wydawcy i typu w celu określenia rozszerzenia, które ma zostać zablokowane.

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

Jeśli masz licencję lokalną, możesz zapisać opłatę za licencję na maszynach wirtualnych. Jeśli nie masz licencji, należy zabronić opcji. Następujące zasady uniemożliwiają korzystanie z korzyści z używania hybrydowej platformy Azure (— AHUB):

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
* Po zdefiniowaniu reguły zasad (jak pokazano w poprzednich przykładach) należy utworzyć definicję zasad i przypisać ją do zakresu. Zakresem może być subskrypcja, Grupa zasobów lub zasób. Aby przypisać zasady, zobacz [używanie Azure Portal do przypisywania zasad zasobów i zarządzania nimi](../../governance/policy/assign-policy-portal.md), [Używanie programu PowerShell do przypisywania zasad](../../governance/policy/assign-policy-powershell.md)lub [Używanie interfejsu wiersza polecenia platformy Azure do przypisywania zasad](../../governance/policy/assign-policy-azurecli.md).
* Aby zapoznać się z wprowadzeniem do zasad zasobów, zobacz [co to jest Azure Policy?](../../governance/policy/overview.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).
