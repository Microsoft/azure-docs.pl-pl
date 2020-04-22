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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680791"
---
W tej sekcji opisano sposób wykonywania następujących operacji modyfikacji dla komunikacji bezpośredniej komunikacji równorzędnej:

* Dodawanie bezpośrednich połączeń komunikacji równorzędnej.
* Usuń bezpośrednie połączenia równorzędne.
* Uaktualnianie lub obniżanie przepustowości w połączeniach aktywnych.
* Dodawanie sesji IPv4 lub IPv6 w aktywnych połączeniach.
* Usuń sesje IPv4 lub IPv6 w aktywnych połączeniach.

### <a name="add-direct-peering-connections"></a>Dodawanie bezpośrednich połączeń komunikacji równorzędnej

W tym przykładzie opisano sposób dodawania połączeń do istniejącej komunikacji bezpośredniej komunikacji równorzędnej.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Usuwanie bezpośrednich połączeń komunikacji równorzędnej

Usuwanie połączenia nie jest obecnie obsługiwane w programie PowerShell. Aby uzyskać więcej informacji, skontaktuj się z [programem Microsoft peering](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Uaktualnianie lub obniżanie przepustowości połączeń aktywnych

W tym przykładzie opisano sposób dodawania 10 Gb/s do istniejącego połączenia bezpośredniego.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>Dodawanie sesji IPv4 lub IPv6 w aktywnych połączeniach

W tym przykładzie opisano sposób dodawania sesji IPv6 w istniejącym połączeniu bezpośrednim tylko z sesją IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>Usuwanie sesji IPv4 lub IPv6 w aktywnych połączeniach

Usuwanie sesji IPv4 lub IPv6 z istniejącego połączenia nie jest obecnie obsługiwane w programie PowerShell. Aby uzyskać więcej informacji, skontaktuj się z [programem Microsoft peering](mailto:peeringexperience@microsoft.com).