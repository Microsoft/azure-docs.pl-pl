---
title: Kontrolki zabezpieczeń dla usługi Azure Event Hubs
description: Ten artykuł zawiera listę kontrolną kontroli zabezpieczeń służącą do oceny Event Hubs platformy Azure (sieci, tożsamości, ochrony danych itp.).
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 0769e88eb72b5b347dd9ebf4b1634501ca54098e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76309510"
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
| Uwierzytelnianie| Tak | | Autoryzuj [dostęp do usługi Azure Event Hubs](authorize-access-event-hubs.md), [Autoryzuj dostęp do zasobów Event Hubs za pomocą Azure Active Directory](authorize-access-azure-active-directory.md), [autoryzując dostęp do zasobów Event Hubs przy użyciu sygnatur dostępu współdzielonego](authorize-access-shared-access-signature.md) |
| Autoryzacja|  Tak | | [Uwierzytelnianie zarządzanej tożsamości za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs](authenticate-managed-identity.md), [uwierzytelniania aplikacji za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs](authenticate-application.md), [uwierzytelniania dostępu do Event Hubs zasobów przy użyciu sygnatur dostępu współdzielonego (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft |  Tak | |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak. Dostępne dla dedykowanych klastrów. | Klucz zarządzany przez klienta w magazynie kluczy platformy Azure może służyć do szyfrowania danych w centrum zdarzeń w stanie spoczynku. | [Skonfiguruj klucze zarządzane przez klienta do szyfrowania danych Event Hubs platformy Azure przechowywanych przy użyciu Azure Portal](configure-customer-managed-key.md) |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Nie dotyczy | |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | |  |
| Wywołania interfejsu API są szyfrowane| Tak |  |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | |  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).
