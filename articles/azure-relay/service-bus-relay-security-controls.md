---
title: Kontrolki zabezpieczeń dla Azure Relay
description: W tym artykule przedstawiono listę kontrolną wbudowanych mechanizmów zabezpieczeń do oceniania Azure Relay.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: f8165d994e998af4f15cd6aa2fd08b75191b8b64
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211463"
---
# <a name="security-controls-for-azure-relay"></a>Kontrolki zabezpieczeń dla Azure Relay

W tym artykule opisano kontrolki zabezpieczeń wbudowane w Azure Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Obsługa punktów końcowych usługi| Nie |  |   |
| Izolacja sieci i obsługa zapór| Nie |  |   |
| Obsługa tunelowania wymuszonego| Brak | Przekaźnik jest tunelem TLS  |   |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Yes | |   |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes | Za [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Rejestrowanie i inspekcja płaszczyzny danych| Yes | Powodzenie/Niepowodzenie połączenia i błędy oraz zarejestrowane.  |   |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Authentication| Yes | Za pośrednictwem SAS. | [Azure Relay uwierzytelnianie i autoryzacja](relay-authentication-and-authorization.md) |
| Autoryzacja|  Yes | Za pośrednictwem SAS. | [Azure Relay uwierzytelnianie i autoryzacja](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft |  Brak | Przekaźnik jest gniazdem internetowym i nie utrzymuje danych. |   |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie | Używa tylko certyfikatów Microsoft TLS.  |   |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Brak | |   |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Yes | Usługa wymaga protokołu TLS. |   |
| Wywołania interfejsu API są szyfrowane| Yes | Schemat. |


## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes | Za [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).