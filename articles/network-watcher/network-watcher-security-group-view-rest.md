---
title: Analizowanie zabezpieczeń sieci — widok grupy zabezpieczeń — interfejs API REST platformy Azure
titleSuffix: Azure Network Watcher
description: W tym artykule opisano, jak interfejs API REST platformy Azure analizuje zabezpieczenia maszyn wirtualnych za pomocą widoku grupy zabezpieczeń.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 2efd3e9c9ca97ea3d94b03bd5e440cd24d5da5da
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960607"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analizowanie zabezpieczeń maszyny wirtualnej za pomocą widoku grupy zabezpieczeń przy użyciu interfejsu API REST

> [!div class="op_single_selector"]
> - [Program PowerShell](network-watcher-security-group-view-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-security-group-view-cli.md)
> - [Interfejs API REST](network-watcher-security-group-view-rest.md)

> [!NOTE]
> Interfejs API widoku grupy zabezpieczeń nie jest już obsługiwany i zostanie wkrótce wycofany. Użyj funkcji obowiązujących [reguł zabezpieczeń](./network-watcher-security-group-view-overview.md) , która zapewnia te same funkcje. 

Widok grupy zabezpieczeń umożliwia skonfigurowanie skonfigurowanych i obowiązujących reguł zabezpieczeń sieci, które są stosowane do maszyny wirtualnej. Ta funkcja jest przydatna do inspekcji i diagnozowania sieciowych grup zabezpieczeń i reguł skonfigurowanych na maszynie wirtualnej w celu zapewnienia, że ruch jest prawidłowo dozwolony lub odrzucany. W tym artykule pokazano, jak pobrać obowiązujące i stosowane reguły zabezpieczeń do maszyny wirtualnej przy użyciu interfejsu API REST


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu należy wywołać interfejs API REST Network Watcher, aby uzyskać widok grupy zabezpieczeń dla maszyny wirtualnej. ARMclient jest używany do wywoływania interfejsu API REST przy użyciu programu PowerShell. ARMClient można znaleźć na czekolady w [ARMClient na czekoladie](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu założono, że wykonano już kroki opisane w temacie [tworzenie Network Watcher](network-watcher-create.md) w celu utworzenia Network Watcher. W tym scenariuszu założono również, że grupa zasobów z prawidłową maszyną wirtualną istnieje do użycia.

## <a name="scenario"></a>Scenariusz

Scenariusz opisany w tym artykule pobiera obowiązujące i stosowane reguły zabezpieczeń dla danej maszyny wirtualnej.

## <a name="log-in-with-armclient"></a>Logowanie za pomocą ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Pobierz maszynę wirtualną

Uruchom następujący skrypt, aby zwrócić wirtualne machineThe następujące kod wymaga zmiennych:

- **subskrypcji** — Identyfikator subskrypcji można także pobrać przy użyciu polecenia cmdlet **Get-AzSubscription** .
- **resourceGroupName** — nazwa grupy zasobów zawierającej maszyny wirtualne.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Informacje, które są zbędne, to **Identyfikator** pod typem `Microsoft.Compute/virtualMachines` w odpowiedzi, jak pokazano w poniższym przykładzie:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>Pobierz widok grupy zabezpieczeń dla maszyny wirtualnej

Poniższy przykład żąda widoku grupy zabezpieczeń dla dostosowanej maszyny wirtualnej. Wyniki z tego przykładu mogą służyć do porównania z regułami i zabezpieczeniami zdefiniowanymi przez źródło, aby wyszukać dryf konfiguracji.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Wyświetlanie odpowiedzi

Poniższy przykład to odpowiedź zwrócona z poprzedniego polecenia. Wyniki pokazują wszystkie obowiązujące i stosowane reguły zabezpieczeń na maszynie wirtualnej podzielone na grupy **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** i **EffectiveSecurityRules**.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

Odwiedź stronę [Inspekcja sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) z Network Watcher](network-watcher-security-group-view-powershell.md) , aby dowiedzieć się, jak zautomatyzować sprawdzanie poprawności sieciowych grup zabezpieczeń.