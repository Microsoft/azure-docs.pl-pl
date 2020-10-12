---
title: plik dołączany
titleSuffix: Azure
description: plik dołączany
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81680750"
---
Polecenie cmdlet **Get-AzPeeringLocation** programu PowerShell zwraca listę lokalizacji komunikacji równorzędnej z obowiązkowym parametrem `Kind` , który będzie używany w dalszych krokach.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Bezpośrednie lokalizacje komunikacji równorzędnej zawierają następujące pola:
* PeeringLocation 
* Kraj
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

Sprawdź, czy istnieje w wymaganej funkcji komunikacji równorzędnej, odwołując się do [PeeringDB](https://wwww.peeringdb.com).

Ten przykład pokazuje, jak używać Seattle jako lokalizacji komunikacji równorzędnej do tworzenia bezpośredniej komunikacji równorzędnej.

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