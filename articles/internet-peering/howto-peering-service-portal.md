---
title: Włączanie usługi równorzędnej platformy Azure w bezpośredniej komunikacji równorzędnej przy użyciu witryny Azure portal
titleSuffix: Azure
description: Włączanie usługi równorzędnej platformy Azure w bezpośredniej komunikacji równorzędnej przy użyciu witryny Azure portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 625a17e5acff00f78c5a19725653eec629936f87
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687044"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Włączanie usługi równorzędnej platformy Azure w bezpośredniej komunikacji równorzędnej przy użyciu witryny Azure portal

W tym artykule opisano sposób [włączania usługi równorzędnej platformy](overview-peering-service.md) Azure w bezpośredniej komunikacji równorzędnej przy użyciu witryny Azure portal.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą programu [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) przed rozpoczęciem konfiguracji.
* Wybierz bezpośrednią komunikację równorzędną w subskrypcji, dla której chcesz włączyć usługę komunikacji równorzędnej. Jeśli go nie masz, przekonwertuj starszą komunikację równorzędną direct lub utwórz nową bezpośrednią komunikację równorzędną:
    * Aby przekonwertować starszą komunikację równorzędną bezpośrednią, postępuj zgodnie z instrukcjami w [obszarze Konwertuj starszą komunikację bezpośrednią równorzędną na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md).
    * Aby utworzyć nową komunikację równorzędną bezpośrednią, postępuj zgodnie z instrukcjami w [pliku Create lub zmodyfikuj bezpośrednie komunikacji równorzędnej za pomocą portalu](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Włączanie usługi Peering Service w bezpośredniej komunikacji równorzędnej

### <a name="view-direct-peering"></a><a name= get></a>Wyświetl bezpośrednią komunikację równorzędnych
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Włączanie usługi komunikacji równorzędnej bezpośredniej dla komunikacji równorzędnej

Po otwarciu direct komunikacji równorzędnej w poprzednim kroku, włącz go dla usługi komunikacji równorzędnej.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modyfikowanie bezpośredniego połączenia równorzędnego

Aby zmodyfikować ustawienia połączenia, zobacz sekcję "Modyfikowanie bezpośredniej komunikacji równorzędnej" w [obszarze Tworzenie lub modyfikowanie bezpośredniego komunikacji równorzędnej za pomocą portalu](howto-direct-portal.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](howto-exchange-portal.md)
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

W przypadku często zadawanych pytań zobacz często zadawane pytania [dotyczące usługi komunikacji równorzędnej](service-faqs.md).