---
title: Włączanie usługi Komunikacja równorzędna Azure w bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell
titleSuffix: Azure
description: Włączanie usługi Komunikacja równorzędna Azure w bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 98341fbbbcafb6aee938870c22050c6edec352ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89079051"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Włączanie usługi Komunikacja równorzędna Azure w bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell

W tym artykule opisano sposób włączania [usługi Komunikacja równorzędna](overview-peering-service.md) Azure w bezpośredniej komunikacji równorzędnej przy użyciu poleceń cmdlet programu PowerShell i modelu wdrażania Azure Resource Manager.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu witryny Azure [Portal](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz
* Przed rozpoczęciem konfiguracji zapoznaj się z [wymaganiami wstępnymi](prerequisites.md) .
* Wybierz bezpośrednią komunikację równorzędną w subskrypcji, dla której chcesz włączyć usługę komunikacji równorzędnej. Jeśli go nie masz, przekonwertuj starszą bezpośrednią komunikację równorzędną lub Utwórz nową bezpośrednią komunikację równorzędną:
    * Aby skonwertować starszą bezpośrednią komunikację równorzędną, postępuj zgodnie z instrukcjami w temacie [konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu programu PowerShell](howto-legacy-direct-powershell.md).
    * Aby utworzyć nową bezpośrednią komunikację równorzędną, postępuj zgodnie z instrukcjami w temacie [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Pracuj z Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Włączanie usługi Peering Service w bezpośredniej komunikacji równorzędnej

### <a name="view-direct-peering"></a><a name= get></a>Wyświetlanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Włącz bezpośrednią komunikację równorzędną dla usługi Komunikacja równorzędna

Po uzyskaniu bezpośredniej komunikacji równorzędnej w poprzednim kroku Włącz ją dla usługi komunikacji równorzędnej.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modyfikowanie bezpośredniego połączenia komunikacji równorzędnej

Jeśli musisz zmodyfikować ustawienia połączenia, zobacz sekcję "Modyfikowanie bezpośredniej komunikacji równorzędnej" w artykule [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell](howto-exchange-powershell.md)
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu programu PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Dodatkowe zasoby
Aby uzyskać szczegółowe opisy wszystkich parametrów, należy uruchomić następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```

W przypadku często zadawanych pytań zobacz [często zadawane pytania dotyczące usługi komunikacji równorzędnej](service-faqs.md).
