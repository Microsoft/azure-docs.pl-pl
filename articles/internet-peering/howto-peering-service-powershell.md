---
title: Włączanie usługi równorzędnej platformy Azure w bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell
titleSuffix: Azure
description: Włączanie usługi równorzędnej platformy Azure w bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c029b822d00ce545e3623a6212421a55b2d1971f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686972"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Włączanie usługi równorzędnej platformy Azure w bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell

W tym artykule opisano sposób [włączania usługi równorzędnej](overview-peering-service.md) platformy Azure w bezpośredniej komunikacji równorzędnej przy użyciu poleceń cmdlet programu PowerShell i modelu wdrażania usługi Azure Resource Manager.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [witryny](howto-peering-service-portal.md)Azure portal .

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) przed rozpoczęciem konfiguracji.
* Wybierz bezpośrednią komunikację równorzędną w subskrypcji, dla której chcesz włączyć usługę komunikacji równorzędnej. Jeśli go nie masz, przekonwertuj starszą komunikację równorzędną direct lub utwórz nową bezpośrednią komunikację równorzędną:
    * Aby przekonwertować starszą komunikację równorzędną bezpośrednią, postępuj zgodnie z instrukcjami w [obszarze Konwertuj starszą komunikację bezpośrednią równorzędną na zasób platformy Azure przy użyciu programu PowerShell](howto-legacy-direct-powershell.md).
    * Aby utworzyć nową komunikację równorzędną bezpośrednią, postępuj zgodnie z instrukcjami w [programie Tworzenie lub modyfikowanie komunikacji bezpośredniej równorzędnej przy użyciu programu PowerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Praca z programem Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Włączanie usługi Peering Service w bezpośredniej komunikacji równorzędnej

### <a name="view-direct-peering"></a><a name= get></a>Wyświetl bezpośrednią komunikację równorzędnych
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Włączanie usługi komunikacji równorzędnej bezpośredniej dla komunikacji równorzędnej

Po dokonaniu direct komunikacji równorzędnej w poprzednim kroku, włącz go dla usługi komunikacji równorzędnej.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modyfikowanie bezpośredniego połączenia równorzędnego

Jeśli chcesz zmodyfikować ustawienia połączenia, zobacz sekcję "Modyfikowanie bezpośredniej komunikacji równorzędnej" w [obszarze Tworzenie lub modyfikowanie komunikacji bezpośredniej równorzędnej przy użyciu programu PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell](howto-exchange-powershell.md)
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu programu PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Dodatkowe zasoby
Szczegółowe opisy wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```

W przypadku często zadawanych pytań zobacz często zadawane pytania [dotyczące usługi komunikacji równorzędnej](service-faqs.md).