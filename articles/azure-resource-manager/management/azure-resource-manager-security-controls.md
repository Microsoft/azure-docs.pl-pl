---
title: Mechanizmy zabezpieczeń
description: Lista kontrolna wbudowanych mechanizmów zabezpieczeń do oceny usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: bb8742c38fae88dc1fd1fd1ec175b248f30df3a0
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86054461"
---
# <a name="security-controls-for-azure-resource-manager"></a>Kontrolki zabezpieczeń dla Azure Resource Manager

W tym artykule opisano kontrolki zabezpieczeń wbudowane w Azure Resource Manager.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Tak |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | HTTPS/TLS. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie dotyczy | Azure Resource Manager nie przechowuje zawartości klienta, tylko kontrolują dane. |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Tak | |
| Wywołania interfejsu API są szyfrowane| Tak | |

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Nie | |
| Obsługa iniekcji sieci wirtualnej| Tak | |
| Izolacja sieci i obsługa zapór| Nie |  |
| Obsługa tunelowania wymuszonego| Nie |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Nie | |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Dzienniki aktywności uwidaczniają wszystkie operacje zapisu (PUT, POST, DELETE) wykonywane na zasobach; Zobacz [Wyświetlanie dzienników aktywności w celu inspekcji akcji na zasobach](view-activity-logs.md). |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie dotyczy | |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Na podstawie [Azure Active Directory](../../active-directory/index.yml) .|
| Autoryzacja| Tak | |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak |  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../../security/fundamentals/security-controls.md).
