---
title: Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu programu PowerShell
titleSuffix: Azure
description: Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 5d2a8c910c9e384e137785bc1cd491bc85c7e7a8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678492"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-powershell"></a>Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu programu PowerShell

W tym artykule opisano sposób konwertowania istniejącej starszej funkcji direct komunikacji równorzędnej na zasób platformy Azure przy użyciu poleceń cmdlet programu PowerShell.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [witryny](howto-legacy-direct-portal.md)Azure portal .

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) i bezpośredni przewodnik [komunikacji równorzędnej](walkthrough-direct-all.md) przed rozpoczęciem konfiguracji.

### <a name="work-with-azure-powershell"></a>Praca z programem Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej na zasób platformy Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się na swoje konto platformy Azure i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-a-legacy-direct-peering-for-conversion"></a><a name= get></a>Uzyskaj starszą komunikację równorzędną bezpośrednią w celu konwersji
W tym przykładzie pokazano, jak uzyskać starszą bezpośrednią komunikację równorzędną w lokalizacji komunikacji równorzędnej w Seattle.

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

Oto przykładowa odpowiedź:
```powershell
Name                       :
Sku                        : Basic_Direct_Free
Kind                       : Direct
PeeringLocation            : Seattle
UseForPeeringService       : False
PeerAsn.Id                 :
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.71.156.72/30
PeerSessionIPv4Address     : 4.71.156.73
MicrosoftIPv4Address       : 4.71.156.74
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:2100::1e10/126
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.68.70.140/30
PeerSessionIPv4Address     : 4.68.70.141
MicrosoftIPv4Address       : 4.68.70.142
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:4:3::cc/126
PeerSessionIPv6Address     : 2001:1900:4:3::cd
MicrosoftIPv6Address       : 2001:1900:4:3::ce
SessionStateV6             : Established
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
ProvisioningState          : Succeeded
```

### <a name="convert-a-legacy-direct-peering"></a>Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej

&nbsp;
> [!IMPORTANT]
> Podczas konwertowania starszej komunikacji równorzędnej na zasób platformy Azure modyfikacje nie są obsługiwane. &nbsp;

Użyj tego polecenia, aby przekonwertować starszą komunikację równorzędną direct na zasób platformy Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

Oto przykładowa odpowiedź:

```powershell
Name                 : SeattleDirectPeering
Sku.Name             : Basic_Direct_Free
Kind                 : Direct
Connections          : {11, 11}
PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{asnNumber}
UseForPeeringService : False
PeeringLocation      : Seattle
ProvisioningState    : Succeeded
Location             : centralus
Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
Type                 : Microsoft.Peering/peerings
Tags                 : {}
```

## <a name="additional-resources"></a>Dodatkowe zasoby
Możesz uzyskać szczegółowe opisy wszystkich parametrów, uruchamiając to polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```

Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące komunikacji równorzędnej w Internecie](faqs.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej direct przy użyciu programu PowerShell](howto-direct-powershell.md)
