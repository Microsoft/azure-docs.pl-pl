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
ms.openlocfilehash: 12d169697a35af446392843eb57e6ec3a5508e45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81678641"
---
W przypadku komunikacji równorzędnej programu Exchange obsługiwane są następujące operacje modyfikacji:
* Dodawanie połączeń komunikacji równorzędnej programu Exchange.
* Usuń połączenia komunikacji równorzędnej programu Exchange.
* Dodaj sesję IPv4 lub IPv6 dla aktywnych połączeń.
* Usuń sesję IPv4 lub IPv6 dla aktywnych połączeń.


### <a name="add-exchange-peering-connections"></a>Dodawanie połączeń komunikacji równorzędnej programu Exchange

W tym przykładzie opisano sposób dodawania połączeń do istniejącej komunikacji równorzędnej programu Exchange.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Usuń połączenia komunikacji równorzędnej programu Exchange

W tym przykładzie opisano sposób usuwania połączeń do istniejącej komunikacji równorzędnej programu Exchange.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Wyświetl wszystkie połączenia i wybierz połączenie, które chcesz usunąć. 

```powershell

$exchangePeering

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

W poniższym poleceniu zamiast wartości 0 wprowadź numer indeksu dla połączenia, które chcesz usunąć.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Dodaj sesję IPv4 lub IPv6 dla aktywnych połączeń

W tym przykładzie opisano sposób dodawania sesji IPv6 do istniejącego połączenia z programem Exchange.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Usuń sesję IPv4 lub IPv6 dla aktywnych połączeń

Usuwanie sesji IPv4 lub IPv6 z istniejącego połączenia nie jest obecnie obsługiwane w programie PowerShell. Aby uzyskać więcej informacji, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peeringexperience@microsoft.com).