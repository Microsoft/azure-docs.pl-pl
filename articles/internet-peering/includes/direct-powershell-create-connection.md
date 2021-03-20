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
ms.openlocfilehash: 610bac5c08d7f3aa3c93e273bc6573a08ca1239f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81680761"
---
Poniższy przykład przedstawia sposób tworzenia bezpośredniej komunikacji równorzędnej 10 GB/s w Seattle.

Użyj polecenia cmdlet **New-AzPeeringDirectConnectionObject** programu PowerShell, aby utworzyć obiekty DirectConnection, które mają być używane w nowym żądaniu komunikacji równorzędnej.

Ten przykład pokazuje, jak utworzyć obiekt DirectConnection.

```powershell
$connection1 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.31.0/31 `
    -SessionPrefixV6 fe01::3e:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

> [!NOTE]
> <index>W poprzednim przykładzie wartość **$peeringLocation []** <index> powinna odpowiadać wybranej lokalizacji komunikacji równorzędnej.

Utwórz kolejne połączenie na wypadek potrzeby nadmiarowości w danej lokalizacji komunikacji równorzędnej.

```powershell
$connection2 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.33.0/31 `
    -SessionPrefixV6 fe01::3f:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

Użyj polecenia cmdlet **New-AzPeering** programu PowerShell, aby utworzyć nową bezpośrednią komunikację równorzędną. To polecenie wymaga identyfikatora zasobu ASN, który można pobrać, jak pokazano tutaj.


```powershell
$asn = Get-AzPeerAsn
New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `
    -PeeringLocation  $peeringLocation[0].PeeringLocation `
    -PeerAsnResourceId $asn.Id `
    -DirectConnection $connection1 [, $connection2]
```
&nbsp;

Ten przykład przedstawia odpowiedź, gdy żądanie zostało przetworzone pomyślnie.

```powershell

    Name                 : SeattleDirectPeering
    Sku.Name             : Basic_Direct_Free
    Kind                 : Direct
    Connections          : 71
    PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/SeattleDirectPeering
    UseForPeeringService : False
    PeeringLocation      : Seattle
    ProvisioningState    : Succeeded
    Location             : centralus
    Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
    Type                 : Microsoft.Peering/peerings
    Tags                 : {}

```
Należy pamiętać, że zamiast elementu **{Subscription}** w tym wyniku zostanie wyświetlony rzeczywisty identyfikator subskrypcji.
