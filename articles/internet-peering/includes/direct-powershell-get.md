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
ms.openlocfilehash: c84cd8b1d7850576eb608d88ba91c3079edb0d68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680797"
---
Aby uzyskać listę komunikacji równorzędnych, uruchom polecenie **Get-AzPeering** .

```powershell
$directPeering = Get-AzPeering -ResourceGroupName "PeeringResourceGroup" -Name "SeattleDirectPeering"
```

Poniższy przykład przedstawia odpowiedź, gdy kompleksowe Inicjowanie obsługi administracyjnej zostało zakończone pomyślnie.

```powershell
    Name                 : SeattleDirectPeering
    Sku.Name             : Basic_Direct_Free
    Kind                 : Direct
    Connections          : {71}
    PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/SeattleDirectPeering
    UseForPeeringService : False
    PeeringLocation      : Seattle
    ProvisioningState    : Succeeded
    Location             : centralus
    Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
    Type                 : Microsoft.Peering/peerings
    Tags                 : {}
```