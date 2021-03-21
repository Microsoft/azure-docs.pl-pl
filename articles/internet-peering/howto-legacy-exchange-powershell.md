---
title: Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu programu PowerShell
titleSuffix: Azure
description: Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: acc32f4916f5f7f8fe22eebdd1e72db297cac94c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590209"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu programu PowerShell

W tym artykule opisano sposób konwertowania istniejącej starszej komunikacji równorzędnej programu Exchange do zasobu platformy Azure przy użyciu poleceń cmdlet programu PowerShell.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu witryny Azure [Portal](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz
* Przed rozpoczęciem konfiguracji zapoznaj się z tematem [wymagania wstępne](prerequisites.md) i [Przewodnik po komunikacji równorzędnej programu Exchange](walkthrough-exchange-all.md) .

### <a name="work-with-azure-powershell"></a>Pracuj z Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Pobieranie starszej wersji komunikacji równorzędnej programu Exchange na potrzeby konwersji
Ten przykład pokazuje, jak uzyskać starszą komunikację równorzędną programu Exchange w lokalizacji komunikacji równorzędnej w Seattle:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

Odpowiedź wygląda podobnie do poniższego przykładu:
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

### <a name="convert-legacy-peering"></a>Konwertuj starszą komunikację równorzędną
To polecenie służy do konwertowania starszej komunikacji równorzędnej programu Exchange do zasobu platformy Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> W przypadku konwertowania starszej komunikacji równorzędnej na zasób platformy Azure modyfikacje nie są obsługiwane.
&nbsp;

Ta przykładowa odpowiedź pokazuje, kiedy kompleksowe Inicjowanie obsługi zostało pomyślnie zakończone:

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
Aby uzyskać szczegółowe opisy wszystkich parametrów, należy uruchomić następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```
Aby uzyskać więcej informacji, zobacz temat [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell](howto-exchange-powershell.md)
