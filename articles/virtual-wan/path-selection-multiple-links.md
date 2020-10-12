---
title: Wybór ścieżki platformy Azure dla wielu linków usługodawcy internetowego
titleSuffix: Azure Virtual WAN
description: Informacje o wyborze ścieżki platformy Azure i wirtualnej sieci WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: f24696c0db3155a59106e1361b01454b9ac16a20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267758"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>Wybór ścieżki platformy Azure dla wielu linków usługodawcy internetowego

Wirtualna sieć WAN platformy Azure umożliwia użytkownikowi dołączenie informacji o linku w witrynie sieci VPN, co umożliwia scenariusze, w których rozwiązanie sieci VPN/SD-WAN może programować zasady specyficzne dla gałęzi w celu kierowania ruchu między różnymi łączami do platformy Azure. Jest to tzw. **Wybór ścieżki platformy Azure**.

## <a name="architecture"></a>Architektura

Aby zrozumieć, jak działa wybór ścieżki platformy Azure, użyjmy przykładowej wirtualnej sieci WAN i połączenia lokacja-lokacja.

Lokacja sieci VPN reprezentuje lokalne urządzenie SD-WAN/VPN z informacjami, takimi jak publiczny adres IP, model i nazwa urządzenia itp. Rzeczywista Lokalna lokacja sieci VPN może zawierać wiele linków usługodawców internetowych, które mogą być również zawarte w informacjach o lokacji wirtualnej sieci WAN. Pozwala to wyświetlić informacje o linku na platformie Azure.

Połączenie IPsec typu lokacja-lokacja w sieci VPN wirtualnej sieci WAN kończy się na wystąpieniach bramy sieci VPN w koncentratorze wirtualnym. Połączenie typu lokacja-lokacja reprezentuje połączenie między lokacją sieci VPN i bramą sieci VPN platformy Azure. Składa się z jednego lub kilku połączeń linków. Każde połączenie linku składa się z dwóch tuneli, z których każdy tunel kończy się na unikatowym wystąpieniu bramy sieci VPN wirtualnej platformy Azure. Do połączenia typu lokacja-lokacja można skonfigurować maksymalnie cztery połączenia z linkami, dzięki czemu możliwe jest posiadanie maksymalnie ośmiu tuneli w ramach połączenia lokacja-lokacja. Platforma Azure obsługuje do 2000 tuneli kończące się w ramach jednej wirtualnej bramy sieci VPN.

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="Diagram wielołączny":::

Ten rysunek przedstawia wiele łączy w lokacji łączącej się z wirtualną siecią WAN platformy Azure. Na tym diagramie:

* Istnieją dwa linki usługodawcy internetowego na gałęzi lokalnej (urządzenie sieci VPN/SD-WAN). Każde łącze usługodawcy internetowego odpowiada połączeniu z linkiem.

* Przyjęto założenie, że lokalne urządzenie sieci VPN/SD-WAN programu Customer Manager obsługuje protokół IKEv1 lub IKEv2 IPsec.

* Każde połączenie wirtualnej sieci WAN platformy Azure typu lokacja-lokacja składa się z połączeń linków wewnątrz siebie. Połączenie obsługuje maksymalnie cztery połączenia linków. Na platformie Azure naliczane są opłaty za jednostkę połączenia wirtualnej sieci WAN. Za połączenia linków nie są naliczane opłaty.

* Każde połączenie łączy z kolei składa się z dwóch tuneli IPsec, które mogą kończyć się dwoma różnymi wystąpieniami wirtualnej bramy sieci VPN. Bramy są ustawiane jako bramy Active-Active w celu zapewnienia odporności. Każde połączenie linku musi mieć unikatowy adres IP i IP komunikacji równorzędnej BGP. Na diagramie tunel 0 może kończyć się wystąpieniem 0, a tunel 1 może zostać przerwany w wystąpieniu 1.

* Urządzenia oddziałów, które udostępniają wybór ścieżki, mogą włączyć odpowiednie zasady w rozwiązaniu do zarządzania gałęziami w celu kierowania ruchu między wieloma łączami do platformy Azure. Na przykład łącze usługodawcy internetowego 1 może być używane w przypadku ruchu o wyższym priorytecie, a łącze usługodawcy internetowego 2 może służyć jako kopia zapasowa.

* Należy pamiętać, że usługa Virtual HUB VPN używa ECMP (równoważna opłata za pośrednictwem routingu) do wszystkich tuneli kończących.

## <a name="next-steps"></a>Następne kroki

Zobacz [często zadawane pytania dotyczące platformy Azure](virtual-wan-faq.md).