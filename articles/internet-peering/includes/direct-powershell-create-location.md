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
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680750"
---
Polecenie cmdlet programu PowerShell **Get-AzPeeringLocation** zwraca listę lokalizacji `Kind`komunikacji równorzędnej z parametrem obowiązkowym, który będzie używany w późniejszych krokach.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Bezpośrednie lokalizacje komunikacji równorzędnej zawierają następujące pola:
* Lokalizacja komunikacji równorzędnej 
* Kraj
* Element PeeringDBFacilityId
* PeeringDBFacilityLink
* TransferOffers

Sprawdź, czy jesteś obecny w żądanej funkcji komunikacji równorzędnej, odwołując się do [PeeringDB](https://wwww.peeringdb.com).

W tym przykładzie pokazano, jak używać Seattle jako lokalizacji komunikacji równorzędnej do tworzenia direct komunikacji równorzędnej.

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```