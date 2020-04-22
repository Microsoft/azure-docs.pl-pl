---
title: Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu programu PowerShell
titleSuffix: Azure
description: Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: eedf87548d62e05d4940911ed3dcd821077acb27
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686792"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu programu PowerShell

W tym artykule opisano sposób konwertowania istniejącej starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu poleceń cmdlet programu PowerShell.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [witryny](howto-legacy-exchange-portal.md)Azure portal .

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) i przewodnik [dla programu Exchange równorzędnej](walkthrough-exchange-all.md) przed rozpoczęciem konfiguracji.

### <a name="work-with-azure-powershell"></a>Praca z programem Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się na swoje konto platformy Azure i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Uzyskaj starszą komunikację równorzędnych programu Exchange w celu konwersji
W tym przykładzie pokazano, jak uzyskać starsze komunikacji równorzędnej programu Exchange w lokalizacji komunikacji równorzędnej w Seattle:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

Odpowiedź wygląda podobnie do następującego przykładu:
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>Konwertowanie starszej komunikacji równorzędnej
To polecenie może służyć do konwersji starszych komunikacji równorzędnej programu Exchange do zasobu platformy Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Podczas konwertowania starszych komunikacji równorzędnej do zasobu platformy Azure modyfikacje nie są obsługiwane.
&nbsp;

W tym przykładzie odpowiedzi pokazano, kiedy end-to-end inicjowania obsługi administracyjnej została pomyślnie zakończona:

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>Dodatkowe zasoby
Szczegółowe opisy wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```
Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące komunikacji równorzędnej w Internecie](faqs.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell](howto-exchange-powershell.md)
