---
title: Kontrolki zabezpieczeń dla Azure Service Bus Relay
description: Ten artykuł zawiera listę kontrolną wbudowanych mechanizmów zabezpieczeń do oceny Azure Service Bus Relay.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 28d3ba14aa7769ac4f3fc22bd2b5bd7acd30557c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76514021"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Kontrolki zabezpieczeń dla Azure Service Bus Relay

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w Azure Service Bus Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Obsługa punktów końcowych usługi| Nie |  |   |
| Izolacja sieci i obsługa zapór| Nie |  |   |
| Obsługa tunelowania wymuszonego| Nie dotyczy | Przekaźnik jest tunelem TLS  |   |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | |   |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Za [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Rejestrowanie i inspekcja płaszczyzny danych| Tak | Powodzenie/Niepowodzenie połączenia i błędy oraz zarejestrowane.  |   |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Uwierzytelnianie| Tak | Za pośrednictwem SAS. | [Azure Relay uwierzytelnianie i autoryzacja](relay-authentication-and-authorization.md) |
| Autoryzacja|  Tak | Za pośrednictwem SAS. | [Azure Relay uwierzytelnianie i autoryzacja](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft |  Nie dotyczy | Przekaźnik jest gniazdem internetowym i nie utrzymuje danych. |   |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie | Używa tylko certyfikatów Microsoft TLS.  |   |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Nie dotyczy | |   |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Usługa wymaga protokołu TLS. |   |
| Wywołania interfejsu API są szyfrowane| Tak | Schemat. |


## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | Za [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).