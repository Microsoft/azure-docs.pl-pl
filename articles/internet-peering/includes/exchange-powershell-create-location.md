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
ms.openlocfilehash: fa8d8ccef7d6ad6e1b5d9f19de61e45ee8c439fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "82587848"
---
Polecenie cmdlet **Get-AzPeeringLocation** programu PowerShell zwraca listę lokalizacji komunikacji równorzędnej z obowiązkowym parametrem `Kind` , który będzie używany w dalszych krokach.

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Lokalizacje komunikacji równorzędnej programu Exchange zawierają następujące pola:
* Program exchangename
* PeeringLocation
* Kraj
* PeeringDBFacilityId
* PeeringDBFacilityLink
* MicrosoftIPv4Address
* MicrosoftIPv6Address

Sprawdź, czy istnieje w wymaganej funkcji komunikacji równorzędnej, odwołując się do [PeeringDB](https://www.peeringdb.com).

Ten przykład pokazuje, jak używać Seattle jako lokalizacji komunikacji równorzędnej do tworzenia komunikacji równorzędnej.

```powershell
$exchangeLocations = Get-AzPeeringLocation -Kind Exchange
$exchangeLocation = $exchangeLocations | where {$_.PeeringLocation -eq "Seattle"}

#check the location metadata
$exchangeLocation

ExchangeName          : Columbia IX
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 99999
PeeringDBFacilityLink : https://www.peeringdb.com/ix/99999
MicrosoftIPv4Address  : 10.12.97.129
MicrosoftIPv6Address  :

ExchangeName          : Equinix Seattle
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 11
PeeringDBFacilityLink : https://www.peeringdb.com/ix/11
MicrosoftIPv4Address  : 198.32.134.152
MicrosoftIPv6Address  : 2001:504:12::15

...

```
