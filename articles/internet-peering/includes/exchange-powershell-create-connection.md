---
title: Plik dyrektywy include
titleSuffix: Azure
description: Plik dyrektywy include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: f8e93cf34ac56344ff7e3d145ce8c7c3529767b7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678621"
---
W poniższym przykładzie pokazano, jak utworzyć połączenie programu Exchange w equinix Internet Exchange w Seattle. Jeśli używasz innego dostawcy i różnych ustawień, zastąp te informacje podczas składania wniosku.

Użyj polecenia cmdlet programu PowerShell **New-AzPeeringExchangeConnectionObject** do utworzenia obiektów połączenia programu PowerShell, które będą używane do generowania nowego żądania komunikacji równorzędnej.

W tym przykładzie pokazano, jak utworzyć połączenie programu Exchange.

```powershell
$connection1 = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `
```

Utwórz inne połączenie w przypadku, gdy potrzebujesz nadmiarowości w danej lokalizacji komunikacji równorzędnej.

```powershell
$connection2 = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.23 `
    -PeerSessionIPv6Address  2001:504:12::23 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `
```

Polecenie cmdlet programu PowerShell **New-AzPeering** może służyć do tworzenia nowego programu Exchange komunikacji równorzędnej.

```powershell
$asn = Get-AzPeerAsn
New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup" `
    -PeerAsnResourceId $asn.Id `
    -PeeringLocation  $exchangeLocation[1].PeeringLocation `
    -ExchangeConnection $connection1[, $connection2]
```
&nbsp;

W tym przykładzie odpowiedzi pokazano, kiedy żądanie zostało wykonane przy użyciu jednego połączenia.

```powershell

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

> [!IMPORTANT]
> Firma Microsoft rozpoczyna inicjowanie obsługi `ConnectionState` administracyjnej żądanej komunikacji równorzędnej i odzwierciedla postęp.
> Aby uzyskać więcej informacji na temat kroków związanych z inicjowania obsługi administracyjnej, zobacz przewodnik [komunikacji równorzędnej programu Exchange](../walkthrough-exchange-all.md).

Możesz sprawdzić stan połączenia, jak pokazano poniżej.

```powershell

$peering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"
$peering.Connections

PeeringDBFacilityId         : 11
PeerSessionIPv4Address      : 198.32.134.22
PeerSessionIPv6Address      : 2001:504:12::22
SessionStateV4              : PendingAdd
SessionStateV6              : PendingAdd
MaxPrefixesAdvertisedV4     : 2000
MaxPrefixesAdvertisedV6     : 2000
MicrosoftSessionIPv4Address : 198.32.134.152
MicrosoftSessionIPv4Address : 2001:504:12::15
Md5AuthenticationKey        :

```