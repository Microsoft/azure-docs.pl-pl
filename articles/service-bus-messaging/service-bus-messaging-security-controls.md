---
title: Kontrolki zabezpieczeń dla Azure Service Bus Messaging
description: Lista kontrolna kontroli zabezpieczeń służąca do oceniania Azure Service Bus komunikatów
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3130150a227076befae3f58f65e00a36578b68d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85341638"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Kontrolki zabezpieczeń dla Azure Service Bus Messaging

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w Azure Service Bus Messaging.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Obsługa punktów końcowych usługi| Tak (tylko warstwa Premium) | Punkty końcowe usługi sieci wirtualnej są obsługiwane tylko w przypadku [Service Bus warstwy Premium](service-bus-premium-messaging.md) . |  |
| Obsługa iniekcji sieci wirtualnej| Nie | |  |
| Izolacja sieci i obsługa zapór| Tak (tylko warstwa Premium) |  |  |
| Obsługa tunelowania wymuszonego| Nie |  |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Obsługiwane za pośrednictwem [Azure monitor i alertów](service-bus-metrics-azure-monitor.md). |  |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Dzienniki operacji są dostępne.  | [Service Bus dzienników diagnostycznych](service-bus-diagnostic-logs.md) |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie |  |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Authentication| Tak | Zarządzane za [Azure Active Directory tożsamość usługi zarządzanej](service-bus-managed-service-identity.md).| [Service Bus uwierzytelniania i autoryzacji](service-bus-authentication-and-authorization.md). |
| Autoryzacja| Tak | Obsługuje autoryzację za pośrednictwem tokenu [RBAC](authenticate-application.md) i SAS. | [Service Bus uwierzytelniania i autoryzacji](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft |  Tak w przypadku szyfrowania po stronie serwera domyślnie. |  |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak. | Klucz zarządzany przez klienta w usłudze Azure Key Recovery może służyć do szyfrowania danych w przestrzeni nazw Service Bus w stanie spoczynku. | [Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania Azure Service Bus danych przechowywanych przy użyciu Azure Portal](configure-customer-managed-key.md)  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Brak | |   |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie VNet-VNet)| Tak | Obsługuje standardowy mechanizm HTTPS/TLS. |   |
| Wywołania interfejsu API są szyfrowane| Tak | Wywołania interfejsu API są nawiązywane za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml) i https. |   |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | Obsługuje przechowywanie wersji dostawcy zasobów za pomocą [interfejsu API Azure Resource Manager](/rest/api/resources/).|   |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).
