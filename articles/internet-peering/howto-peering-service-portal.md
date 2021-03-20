---
title: Włącz usługę komunikacji równorzędnej platformy Azure w bezpośredniej komunikacji równorzędnej przy użyciu Azure Portal
titleSuffix: Azure
description: Włącz usługę komunikacji równorzędnej platformy Azure w bezpośredniej komunikacji równorzędnej przy użyciu Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: a52e6038b622c004dc0d133394cd4f53600b2935
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84700046"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Włącz usługę komunikacji równorzędnej platformy Azure w bezpośredniej komunikacji równorzędnej przy użyciu Azure Portal

W tym artykule opisano sposób włączania [usługi Komunikacja równorzędna](overview-peering-service.md) Azure w bezpośredniej komunikacji równorzędnej przy użyciu Azure Portal.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [programu PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz
* Przed rozpoczęciem konfiguracji zapoznaj się z [wymaganiami wstępnymi](prerequisites.md) .
* Wybierz bezpośrednią komunikację równorzędną w subskrypcji, dla której chcesz włączyć usługę komunikacji równorzędnej. Jeśli go nie masz, przekonwertuj starszą bezpośrednią komunikację równorzędną lub Utwórz nową bezpośrednią komunikację równorzędną:
    * Aby skonwertować starszą bezpośrednią komunikację równorzędną, postępuj zgodnie z instrukcjami w temacie [konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md).
    * Aby utworzyć nową bezpośrednią komunikację równorzędną, postępuj zgodnie z instrukcjami w temacie [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Włączanie usługi Peering Service w bezpośredniej komunikacji równorzędnej

### <a name="view-direct-peering"></a><a name= get></a>Wyświetlanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Włącz bezpośrednią komunikację równorzędną dla usługi Komunikacja równorzędna

Po otwarciu bezpośredniej komunikacji równorzędnej w poprzednim kroku Włącz ją dla usługi komunikacji równorzędnej.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modyfikowanie bezpośredniego połączenia komunikacji równorzędnej

Aby zmodyfikować ustawienia połączenia, zapoznaj się z sekcją "Modyfikowanie bezpośredniej komunikacji równorzędnej" w artykule [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-direct-portal.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](howto-exchange-portal.md)
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

W przypadku często zadawanych pytań zobacz [często zadawane pytania dotyczące usługi komunikacji równorzędnej](service-faqs.md).