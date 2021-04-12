---
title: Planowana konserwacja — Azure Database for PostgreSQL-ze skalowaniem (Citus)
description: W tym artykule opisano funkcję zaplanowanej konserwacji w Azure Database for PostgreSQL-Citus.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: dee7257a296f523dbc9040d65fe68c14edf928f6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106789"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Zaplanowana konserwacja w Azure Database for PostgreSQL — funkcja do skalowania (Citus)

Azure Database for PostgreSQL-Citus) przeprowadza okresową konserwację w celu zapewnienia bezpieczeństwa i stabilności zarządzanej bazy danych.  Podczas konserwacji wszystkie węzły w grupie serwerów uzyskują nowe funkcje, aktualizacje i poprawki.

Najważniejsze funkcje zaplanowanej konserwacji na potrzeby funkcji Citus są następujące:

* Aktualizacje są stosowane w tym samym czasie na wszystkich węzłach w grupie serwerów
* Powiadomienia o zbliżającej się konserwacji są wysyłane do Azure Service Health pięć dni z wyprzedzeniem
* Zwykle istnieje co najmniej 30 dni między pomyślnymi zdarzeniami konserwacji dla grupy serwerów
* Preferowany dzień tygodnia i przedział czasu w tym dniu dla uruchomienia konserwacji można zdefiniować osobno dla każdej grupy serwerów

## <a name="selecting-a-maintenance-window-and-notification-about-upcoming-maintenance"></a>Wybieranie okna obsługi i powiadomienia o zbliżającej się konserwacji

Możesz zaplanować konserwację w określonym dniu tygodnia i przedział czasu w tym dniu. Możesz też umożliwić automatyczne pobranie systemu i przedział czasu. W każdym przypadku system wyśle alert o pięć dni przed uruchomieniem dowolnej konserwacji. System będzie również wiedział, gdy konserwacja jest uruchomiona, i po jej pomyślnym zakończeniu.

Powiadomienia o zbliżającej się zaplanowanej konserwacji są ogłaszane w Azure Service Health i mogą być następujące:

* Wyślij wiadomość e-mail na określony adres
* Wyślij wiadomość e-mail do roli Azure Resource Manager
* Wysyłane w wiadomości tekstowej (SMS) do urządzeń przenośnych
* wypychane jako powiadomienie do aplikacji platformy Azure
* dostarczone jako wiadomość głosowa

Określając preferencje dla harmonogramu konserwacji, można wybrać dzień tygodnia i przedział czasu. Jeśli nie zostanie określony, system będzie wybierać czasy między 23:00 i 7am w czasie regionu grupy serwerów. W ramach subskrypcji platformy Azure można definiować różne harmonogramy dla każdej grupy serwerów z Citus.

> [!IMPORTANT]
> Zwykle istnieje co najmniej 30 dni między pomyślnymi zaplanowanymi zdarzeniami konserwacji dla grupy serwerów.
>
> Jednak w przypadku krytycznej aktualizacji awaryjnej, takiej jak poważna luka w zabezpieczeniach, okno powiadomień może być krótsze, niż pięć dni. Aktualizacja krytyczna może zostać zastosowana do serwera, nawet jeśli konserwacja zaplanowana została wykonana w ciągu ostatnich 30 dni.

Ustawienia planowania można aktualizować w dowolnym momencie. Jeśli zaplanowano konserwację dla grupy serwerów moja skala (Citus) i aktualizujesz harmonogram, istniejące zdarzenia będą kontynuowane zgodnie z harmonogramem. Zmiana ustawień zacznie obowiązywać po pomyślnym zakończeniu istniejących zdarzeń.

Jeśli konserwacja nie powiedzie się lub zostanie anulowana, system utworzy powiadomienie.
Spowoduje to ponowne wypróbowanie konserwacji zgodnie z bieżącymi ustawieniami planowania i powiadomienie przez pięć dni przed następnym zdarzeniem konserwacji.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak zmienić harmonogram konserwacji](howto-hyperscale-maintenance.md)
* Dowiedz się, jak [otrzymywać powiadomienia o nadchodzącej konserwacji](../service-health/service-notifications.md) przy użyciu Azure Service Health
* Dowiedz się, jak [skonfigurować alerty dotyczące nadchodzących zdarzeń konserwacji zaplanowanej](../service-health/resource-health-alert-monitor-guide.md)
