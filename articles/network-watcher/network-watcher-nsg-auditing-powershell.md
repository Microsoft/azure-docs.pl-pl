---
title: Automatyzacja inspekcji sieciowej grupy zabezpieczeń — widok grupy zabezpieczeń
titleSuffix: Azure Network Watcher
description: Ta strona zawiera instrukcje dotyczące konfigurowania inspekcji sieciowej grupy zabezpieczeń
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
ms.openlocfilehash: 177215775c9e83286aa98872eed0ab211a8f36ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94948752"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatyzacja inspekcji sieciowej grupy zabezpieczeń za pomocą widoku grupy zabezpieczeń Network Watcher platformy Azure

Klienci często podlegają wyzwaniom sprawdzającym stan bezpieczeństwa infrastruktury. To wyzwanie nie jest inne dla swoich maszyn wirtualnych na platformie Azure. Należy mieć podobny profil zabezpieczeń oparty na regułach sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń). Korzystając z widoku grupy zabezpieczeń, można teraz uzyskać listę reguł stosowanych do maszyny wirtualnej w ramach sieciowej grupy zabezpieczeń. Można zdefiniować sieciowej grupy ZABEZPIECZEŃy profil zabezpieczeń i zainicjować widok grupy zabezpieczeń na cotygodniowo erze i porównać dane wyjściowe z profilem złota i utworzyć raport. W ten sposób można łatwo zidentyfikować wszystkie maszyny wirtualne, które nie są zgodne z określonym profilem zabezpieczeń.

Jeśli nie znasz sieciowych grup zabezpieczeń, zobacz [Omówienie zabezpieczeń sieci](../virtual-network/network-security-groups-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym scenariuszu należy porównać znaną dobrą linię bazową z wynikami widoku grupy zabezpieczeń zwróconymi dla maszyny wirtualnej.

W tym scenariuszu założono, że wykonano już kroki opisane w temacie [tworzenie Network Watcher](network-watcher-create.md) w celu utworzenia Network Watcher. W tym scenariuszu założono również, że grupa zasobów z prawidłową maszyną wirtualną istnieje do użycia.

## <a name="scenario"></a>Scenariusz

Scenariusz opisany w tym artykule pobiera widok grupy zabezpieczeń dla maszyny wirtualnej.

W tym scenariuszu będziesz:

- Pobierz znany dobry zestaw reguł
- Pobieranie maszyny wirtualnej przy użyciu interfejsu API REST
- Pobierz widok grupy zabezpieczeń dla maszyny wirtualnej
- Oceń odpowiedź

## <a name="retrieve-rule-set"></a>Pobierz zestaw reguł

Pierwszym krokiem w tym przykładzie jest współdziałanie z istniejącą linią bazową. Poniższy przykład to kod JSON wyodrębniony z istniejącej sieciowej grupy zabezpieczeń przy użyciu `Get-AzNetworkSecurityGroup` polecenia cmdlet, które jest używane jako linia bazowa tego przykładu.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Konwertuj zestaw reguł na obiekty programu PowerShell

W tym kroku odczytujemy plik JSON, który został utworzony wcześniej z regułami, które powinny znajdować się w sieciowej grupie zabezpieczeń w tym przykładzie.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Pobierz Network Watcher

Następnym krokiem jest pobranie Network Watcher wystąpienia. `$networkWatcher`Zmienna jest przenoszona do `AzNetworkWatcherSecurityGroupView` polecenia cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>Pobierz maszynę wirtualną

Aby uruchomić polecenie cmdlet w programie, wymagana jest maszyna wirtualna `Get-AzNetworkWatcherSecurityGroupView` . Poniższy przykład pobiera obiekt maszyny wirtualnej.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Pobierz widok grupy zabezpieczeń

Następnym krokiem jest pobranie wyniku widoku grupy zabezpieczeń. Ten wynik jest porównywany z formatem JSON "Baseline", który został pokazany wcześniej.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analizowanie wyników

Odpowiedź jest pogrupowana według interfejsów sieciowych. Różne typy zwracanych reguł są obowiązujące i domyślne reguły zabezpieczeń. Wynik jest w dalszej postaci podzielony na sposób ich stosowania — w podsieci lub wirtualnej karcie sieciowej.

Poniższy skrypt programu PowerShell porównuje wyniki widoku grupy zabezpieczeń z istniejącymi danymi wyjściowymi sieciowej grupy zabezpieczeń. Poniższy przykład to prosty przykład sposobu, w jaki wyniki można porównać z `Compare-Object` poleceniem cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

Poniższy przykład to wynik. W porównaniu można zobaczyć dwie z reguł, które znajdowały się w pierwszym zestawie reguł.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Następne kroki

Jeśli ustawienia zostały zmienione, zobacz [Manage Network Security Groups](../virtual-network/manage-network-security-group.md) to Track The Network Security Groups and Security rules.