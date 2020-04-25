---
title: Kontrolki zabezpieczeń dla usługi Azure VPN Gateway
description: Lista kontrolna zabezpieczeń na potrzeby oceny VPN Gateway platformy Azure
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6fc5b4c901254decdb2d34281a10ababd4d79d45
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127853"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Kontrolki zabezpieczeń dla usługi Azure VPN Gateway

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w usługę Azure VPN Gateway.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Nie dotyczy | |
| Obsługa iniekcji sieci wirtualnej| Nie dotyczy | |
| Izolacja sieci i obsługa zapór| Yes | Bramy sieci VPN to dedykowane wystąpienia maszyn wirtualnych dla poszczególnych klientów Virtual Network  |
| Obsługa tunelowania wymuszonego| Yes |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Yes | Zobacz alert dotyczący [dziennika](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & Azure monitor[Azure monitor alertu metryki](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes | Azure Resource Manager dziennik aktywności. |
| Rejestrowanie i inspekcja płaszczyzny danych | Yes | [Azure monitor dzienniki aktywności](../azure-resource-manager/management/view-activity-logs.md) na potrzeby rejestrowania i inspekcji łączności sieci VPN. |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) zarządzania usługą i konfigurowania bramy sieci VPN platformy Azure. |
| Autoryzacja| Yes | Obsługa autoryzacji za pośrednictwem [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Nie dotyczy | Dane klienta dotyczące tranzytowej bramy sieci VPN nie przechowują danych klienta |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Yes | Usługa VPN Gateway szyfruje pakiety klienta między bramami sieci VPN platformy Azure i lokalnymi urządzeniami sieci VPN (S2S) lub klientami sieci VPN (P2S). Bramy VPN obsługują również szyfrowanie między sieciami wirtualnymi. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie | Klucze wstępne określone przez klienta są szyfrowane w stanie spoczynku; ale nie jest to jeszcze zintegrowane z CMK. |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Nie dotyczy | |
| Wywołania interfejsu API są szyfrowane| Yes | Za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml) i https  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes | W przypadku operacji zarządzania stanem konfiguracji bramy sieci VPN platformy Azure można wyeksportować jako szablon Azure Resource Manager i wersję z biegiem czasu. |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).
