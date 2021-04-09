---
title: Planowana konserwacja — Azure Database for PostgreSQL-ze skalowaniem (Citus)
description: W tym artykule opisano funkcję zaplanowanej konserwacji w Azure Database for PostgreSQL-Citus.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0d11ec8815cc0f5082f95c5331321f043e86eece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027676"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Zaplanowana konserwacja w Azure Database for PostgreSQL — funkcja do skalowania (Citus)

Azure Database for PostgreSQL-Citus) przeprowadza okresową konserwację w celu zapewnienia bezpieczeństwa i stabilności zarządzanej bazy danych.  Podczas konserwacji wszystkie węzły w grupie serwerów uzyskują nowe funkcje, aktualizacje i poprawki.

Najważniejsze funkcje zaplanowanej konserwacji na potrzeby funkcji Citus są następujące:

* Aktualizacje są stosowane w tym samym czasie na wszystkich węzłach w grupie serwerów
* Powiadomienia o zbliżającej się konserwacji są wysyłane do Azure Service Health pięć dni z wyprzedzeniem
* Zwykle istnieje co najmniej 30 dni między pomyślnymi zdarzeniami konserwacji dla grupy serwerów

## <a name="notification-about-upcoming-maintenance"></a>Powiadomienie o zbliżającej się konserwacji

Powiadomienia o zbliżającej się zaplanowanej konserwacji są ogłaszane w Azure Service Health i mogą być następujące:

* Wyślij wiadomość e-mail na określony adres
* Wyślij wiadomość e-mail do roli Azure Resource Manager
* Wysyłane w wiadomości tekstowej (SMS) do urządzeń przenośnych
* wypychane jako powiadomienie do aplikacji platformy Azure
* dostarczone jako wiadomość głosowa

> [!IMPORTANT]
> Zwykle istnieje co najmniej 30 dni między pomyślnymi zaplanowanymi zdarzeniami konserwacji dla grupy serwerów.
>
> Jednak w przypadku krytycznej aktualizacji awaryjnej, takiej jak poważna luka w zabezpieczeniach, okno powiadomień może być krótsze, niż pięć dni. Aktualizacja krytyczna może zostać zastosowana do serwera, nawet jeśli konserwacja zaplanowana została wykonana w ciągu ostatnich 30 dni.

Jeśli konserwacja nie powiedzie się lub zostanie anulowana, system utworzy powiadomienie.
Spowoduje to ponowne wypróbowanie konserwacji zgodnie z bieżącymi ustawieniami planowania i powiadomienie przez pięć dni przed następnym zdarzeniem konserwacji.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [otrzymywać powiadomienia o nadchodzącej konserwacji](../service-health/service-notifications.md) przy użyciu Azure Service Health
* Dowiedz się, jak [skonfigurować alerty dotyczące nadchodzących zdarzeń konserwacji zaplanowanej](../service-health/resource-health-alert-monitor-guide.md)
