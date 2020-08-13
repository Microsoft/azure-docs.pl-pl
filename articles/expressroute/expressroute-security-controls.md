---
title: 'Azure ExpressRoute: kontrolki zabezpieczeń'
description: Dowiedz się więcej o kontrolkach zabezpieczeń w usłudze Azure ExpressRoute, które są właściwościami i funkcjami, które pomagają zapobiegać lukami w zabezpieczeniach, wykrywać je i reagować na nie
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 6a4589d1aa768548f2ae9ffa01f289c823c0b2e5
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192415"
---
# <a name="security-controls-for-azure-expressroute"></a>Kontrolki zabezpieczeń dla usługi Azure ExpressRoute

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w usługę Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Nie dotyczy |  |
| Obsługa iniekcji sieci wirtualnej| Nie dotyczy | |
| Izolacja sieci i obsługa zapór| Tak | Każdy klient jest zawarty we własnej domenie routingu i tunelowany do własnej sieci wirtualnej |
| Obsługa tunelowania wymuszonego| Nie dotyczy | Za pośrednictwem Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Zobacz [monitorowanie ExpressRoute, metryki i alerty](expressroute-monitoring-metrics-alerts.md).|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak |  |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie |   |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Konto usługi dla bramy dla firmy Microsoft (GWM) (kontroler); Dostęp just in Time (JIT) do deweloperów i operacji. |
| Autoryzacja|  Tak |Konto usługi dla bramy dla firmy Microsoft (GWM) (kontroler); Dostęp just in Time (JIT) do deweloperów i operacji. |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft |  Nie dotyczy | ExpressRoute nie przechowuje danych klienta. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie dotyczy |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Nie dotyczy | |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Nie | |
| Wywołania interfejsu API są szyfrowane| Tak | Za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml) i https. |


## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | Za pośrednictwem dostawcy zasobów sieciowych (NRP). |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).