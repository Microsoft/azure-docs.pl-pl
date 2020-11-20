---
title: Analizowanie zabezpieczeń sieci — widok grupy zabezpieczeń-Azure PowerShell
titleSuffix: Azure Network Watcher
description: W tym artykule opisano, jak za pomocą programu PowerShell analizować zabezpieczenia maszyn wirtualnych za pomocą widoku grupy zabezpieczeń.
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
ms.openlocfilehash: 2da4ebfa6a9ce64b03e6e1d29956f740f6cb3c1b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960636"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analizowanie zabezpieczeń maszyny wirtualnej za pomocą widoku grupy zabezpieczeń przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Program PowerShell](network-watcher-security-group-view-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-security-group-view-cli.md)
> - [Interfejs API REST](network-watcher-security-group-view-rest.md)

> [!NOTE]
> Interfejs API widoku grupy zabezpieczeń nie jest już obsługiwany i zostanie wkrótce wycofany. Użyj funkcji obowiązujących [reguł zabezpieczeń](./network-watcher-security-group-view-overview.md) , która zapewnia te same funkcje. 

Widok grupy zabezpieczeń umożliwia skonfigurowanie skonfigurowanych i obowiązujących reguł zabezpieczeń sieci, które są stosowane do maszyny wirtualnej. Ta funkcja jest przydatna do inspekcji i diagnozowania sieciowych grup zabezpieczeń i reguł skonfigurowanych na maszynie wirtualnej w celu zapewnienia, że ruch jest prawidłowo dozwolony lub odrzucany. W tym artykule przedstawiono sposób pobierania skonfigurowanych i obowiązujących reguł zabezpieczeń do maszyny wirtualnej przy użyciu programu PowerShell


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu należy uruchomić `Get-AzNetworkWatcherSecurityGroupView` polecenie cmdlet w celu pobrania informacji o regułach zabezpieczeń.

W tym scenariuszu założono, że wykonano już kroki opisane w temacie [tworzenie Network Watcher](network-watcher-create.md) w celu utworzenia Network Watcher.

## <a name="scenario"></a>Scenariusz

Scenariusz opisany w tym artykule umożliwia pobranie skonfigurowanych i obowiązujących reguł zabezpieczeń dla danej maszyny wirtualnej.

## <a name="retrieve-network-watcher"></a>Pobierz Network Watcher

Pierwszym krokiem jest pobranie wystąpienia Network Watcher. Ta zmienna jest przenoszona do `Get-AzNetworkWatcherSecurityGroupView` polecenia cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>Pobierz maszynę wirtualną

Aby uruchomić polecenie cmdlet w programie, wymagana jest maszyna wirtualna `Get-AzNetworkWatcherSecurityGroupView` . Poniższy przykład pobiera obiekt maszyny wirtualnej.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Pobierz widok grupy zabezpieczeń

Następnym krokiem jest pobranie wyniku widoku grupy zabezpieczeń.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Wyświetlanie wyników

Poniższy przykład to skrócona odpowiedź na zwrócone wyniki. Wyniki pokazują wszystkie obowiązujące i stosowane reguły zabezpieczeń na maszynie wirtualnej podzielone na grupy **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** i **EffectiveSecurityRules**.

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>Następne kroki

Odwiedź stronę [Inspekcja sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) z Network Watcher](network-watcher-nsg-auditing-powershell.md) , aby dowiedzieć się, jak zautomatyzować sprawdzanie poprawności sieciowych grup zabezpieczeń.