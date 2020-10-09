---
title: Kontrolki zabezpieczeń dla usługi Azure Event Hubs
description: Ten artykuł zawiera listę kontrolną kontroli zabezpieczeń służącą do oceny Event Hubs platformy Azure (sieci, tożsamości, ochrony danych itp.).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: da20778f1e24372e445d635e675df6484905f195
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85315387"
---
# <a name="security-controls-for-azure-event-hubs"></a>Kontrolki zabezpieczeń dla usługi Azure Event Hubs

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w usługę Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Obsługa punktów końcowych usługi| Tak |  |  |
| Obsługa iniekcji sieci wirtualnej| Nie | |  |
| Izolacja sieci i obsługa zapór| Tak |  |  |
| Obsługa tunelowania wymuszonego| Nie |  |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | |  |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak |  |  |
| Rejestrowanie i inspekcja płaszczyzny danych| Tak |   |  |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Authentication| Tak | | Autoryzuj [dostęp do usługi Azure Event Hubs](authorize-access-event-hubs.md), [Autoryzuj dostęp do zasobów Event Hubs za pomocą Azure Active Directory](authorize-access-azure-active-directory.md), [autoryzując dostęp do zasobów Event Hubs przy użyciu sygnatur dostępu współdzielonego](authorize-access-shared-access-signature.md) |
| Autoryzacja|  Tak | | [Uwierzytelnianie zarządzanej tożsamości za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs](authenticate-managed-identity.md), [uwierzytelniania aplikacji za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs](authenticate-application.md), [uwierzytelniania dostępu do Event Hubs zasobów przy użyciu sygnatur dostępu współdzielonego (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft |  Tak | |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak. Dostępne dla dedykowanych klastrów. | Klucz zarządzany przez klienta w magazynie kluczy platformy Azure może służyć do szyfrowania danych w centrum zdarzeń w stanie spoczynku. | [Skonfiguruj klucze zarządzane przez klienta do szyfrowania danych Event Hubs platformy Azure przechowywanych przy użyciu Azure Portal](configure-customer-managed-key.md) |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Brak | |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie VNet-VNet)| Tak | |  |
| Wywołania interfejsu API są szyfrowane| Tak |  |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | |  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).
