---
title: Planowana konserwacja — serwer elastyczny Azure Database for MySQL
description: W tym artykule opisano funkcję zaplanowanej konserwacji na serwerze elastycznym Azure Database for MySQL.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: a2e99440a7c8f33eee9d3c9fe2276ac3868ff4b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91331764"
---
# <a name="scheduled-maintenance-in-azure-database-for-mysql--flexible-server"></a>Konserwacja zaplanowana w usłudze Azure Database for MySQL — elastyczny serwer

Azure Database for MySQL — elastyczny serwer przeprowadza okresową konserwację, aby zachować bezpieczeństwo i stabilność zarządzanej bazy danych. Podczas konserwacji serwer pobiera nowe funkcje, aktualizacje i poprawki.

> [!IMPORTANT]
> Azure Database for MySQL — serwer elastyczny jest w wersji zapoznawczej.

## <a name="select-a-maintenance-window"></a>Wybierz okno obsługi

Możesz zaplanować konserwację w określonym dniu tygodnia i przedział czasu w tym dniu. Możesz też zezwolić na automatyczne wybieranie dnia i godziny okna przez system. W każdym przypadku system wyśle alert o pięć dni przed uruchomieniem dowolnej konserwacji. System będzie również wiedział, gdy konserwacja jest uruchomiona, i po pomyślnym zakończeniu.

Powiadomienia dotyczące nadchodzącej zaplanowanej konserwacji mogą być następujące:

* Wyślij wiadomość e-mail na określony adres
* Wyślij wiadomość e-mail do roli Azure Resource Manager
* Wysyłane w wiadomości tekstowej (SMS) do urządzeń przenośnych
* wypychane jako powiadomienie do aplikacji platformy Azure
* dostarczone jako wiadomość głosowa

Określając preferencje dla harmonogramu konserwacji, można wybrać dzień tygodnia i przedział czasu. Jeśli nie zostanie to określone, system wybierze godzinę między 23:00 a 7:00 czasu regionu serwera. Można zdefiniować różne harmonogramy dla każdego elastycznego serwera w ramach subskrypcji platformy Azure.

> [!IMPORTANT]
> Zwykle między pomyślnymi zdarzeniami konserwacji zaplanowanej dla serwera istnieje odstęp co najmniej 30 dni.
>
> Jednak w przypadku krytycznej aktualizacji awaryjnej, takiej jak poważna luka w zabezpieczeniach, okno powiadomień może być krótsze, niż pięć dni. Aktualizacja krytyczna może zostać zastosowana do serwera, nawet jeśli konserwacja zaplanowana została wykonana w ciągu ostatnich 30 dni.

Ustawienia planowania można aktualizować w dowolnym momencie. Jeśli zaplanowano konserwację dla elastycznego serwera i zaktualizujesz preferencje planowania, bieżące zdarzenie będzie obowiązywać zgodnie z harmonogramem, a zmiana ustawień planowania zacznie działać po pomyślnym zakończeniu.

W rzadkich przypadkach zdarzenie konserwacji może zostać anulowane przez system lub może zakończyć się niepowodzeniem. W takim przypadku system utworzy powiadomienie o anulowaniu lub niepomyślnym zdarzeniu konserwacji. Kolejna próba przeprowadzenia konserwacji zostanie zaplanowana zgodnie z bieżącymi ustawieniami planowania i wkrótce otrzymasz powiadomienie o pięciu dniach.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak zmienić harmonogram konserwacji](how-to-maintenance-portal.md)
* Dowiedz się, jak [otrzymywać powiadomienia o nadchodzącej konserwacji](../../service-health/service-notifications.md) przy użyciu Azure Service Health
* Dowiedz się, jak [skonfigurować alerty dotyczące nadchodzących zdarzeń konserwacji zaplanowanej](../../service-health/resource-health-alert-monitor-guide.md)
