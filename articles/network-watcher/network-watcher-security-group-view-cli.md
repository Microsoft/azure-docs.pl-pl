---
title: Analizowanie zabezpieczeń sieci za pomocą widoku grupy zabezpieczeń — interfejs wiersza polecenia platformy Azure
titleSuffix: Azure Network Watcher
description: W tym artykule opisano, jak za pomocą interfejsu wiersza polecenia platformy Azure analizować zabezpieczenia maszyn wirtualnych za pomocą widoku grupy zabezpieczeń.
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
ms.openlocfilehash: b3aa963c4da7802a9db714f25e7b544b3a132d4b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948650"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analizowanie zabezpieczeń maszyny wirtualnej za pomocą widoku grupy zabezpieczeń przy użyciu interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> - [Program PowerShell](network-watcher-security-group-view-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-security-group-view-cli.md)
> - [Interfejs API REST](network-watcher-security-group-view-rest.md)

> [!NOTE]
> Interfejs API widoku grupy zabezpieczeń nie jest już obsługiwany i zostanie wkrótce wycofany. Użyj funkcji obowiązujących [reguł zabezpieczeń](./network-watcher-security-group-view-overview.md) , która zapewnia te same funkcje.


Widok grupy zabezpieczeń umożliwia skonfigurowanie skonfigurowanych i obowiązujących reguł zabezpieczeń sieci, które są stosowane do maszyny wirtualnej. Ta funkcja jest przydatna do inspekcji i diagnozowania sieciowych grup zabezpieczeń i reguł skonfigurowanych na maszynie wirtualnej w celu zapewnienia, że ruch jest prawidłowo dozwolony lub odrzucany. W tym artykule przedstawiono sposób pobierania skonfigurowanych i obowiązujących reguł zabezpieczeń do maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wykonać kroki opisane w tym artykule, należy [zainstalować interfejs wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu założono, że wykonano już kroki opisane w temacie [tworzenie Network Watcher](network-watcher-create.md) w celu utworzenia Network Watcher.

## <a name="scenario"></a>Scenariusz

Scenariusz opisany w tym artykule umożliwia pobranie skonfigurowanych i obowiązujących reguł zabezpieczeń dla danej maszyny wirtualnej.

## <a name="get-a-vm"></a>Pobierz maszynę wirtualną

Aby uruchomić polecenie cmdlet, wymagana jest maszyna wirtualna `vm list` . Następujące polecenie wyświetla listę maszyn wirtualnych w grupie zasobów:

```azurecli
az vm list -resource-group resourceGroupName
```

Po poznaniu maszyny wirtualnej możesz użyć `vm show` polecenia cmdlet, aby uzyskać identyfikator zasobu:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Pobierz widok grupy zabezpieczeń

Następnym krokiem jest pobranie wyniku widoku grupy zabezpieczeń.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Wyświetlanie wyników

Poniższy przykład to skrócona odpowiedź na zwrócone wyniki. Wyniki pokazują wszystkie obowiązujące i stosowane reguły zabezpieczeń na maszynie wirtualnej podzielone na grupy **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** i **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

Odwiedź stronę [Inspekcja sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) z Network Watcher](network-watcher-nsg-auditing-powershell.md) , aby dowiedzieć się, jak zautomatyzować sprawdzanie poprawności sieciowych grup zabezpieczeń.

Dowiedz się więcej o regułach zabezpieczeń, które są stosowane do zasobów sieciowych, odwiedzając [Widok grupy zabezpieczeń omówienie](network-watcher-security-group-view-overview.md)