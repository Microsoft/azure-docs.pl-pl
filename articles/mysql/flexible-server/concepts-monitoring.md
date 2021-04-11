---
title: Monitorowanie — Azure Database for MySQL — elastyczny serwer
description: W tym artykule opisano metryki dotyczące monitorowania i generowania alertów dla Azure Database for MySQL serwera elastycznego, w tym danych statystycznych dotyczących procesora, magazynu i połączeń.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 660e87e0c09d1bf052b20f60276a30e572ab825b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110478"
---
# <a name="monitor-azure-database-for-mysql-flexible-servers-with-built-in-metrics"></a>Monitoruj Azure Database for MySQL elastycznych serwerów za pomocą wbudowanych metryk

> [!IMPORTANT] 
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Azure Database for MySQL elastyczny serwer zapewnia monitorowanie serwerów przez Azure Monitor. Metryki to wartości liczbowe, które opisują pewne aspekty zasobów serwera w określonym czasie. Monitorowanie zasobów serwera ułatwia rozwiązywanie problemów i optymalizowanie obciążeń, umożliwiając monitorowanie najbardziej potrzebnych informacji. Monitorowanie odpowiednich metryk pomaga zapewnić wydajność, niezawodność i dostępność serwera i aplikacji.

W tym artykule opisano różne metryki dostępne dla elastycznego serwera, które zapewniają wgląd w zachowanie serwera.

## <a name="available-metrics"></a>Dostępne metryki

Azure Database for MySQL elastyczny serwer udostępnia różne metryki, aby zrozumieć, jak działa obciążenie i na podstawie tych danych można zrozumieć wpływ na serwer i aplikację. Na przykład w elastycznym serwerze można monitorować **procent procesora CPU hosta**, **aktywne połączenia**, **procent operacji we/wy** i **procent pamięci hosta** , aby określić, kiedy występuje wpływ na wydajność. Z tego miejsca może być konieczne zoptymalizowanie obciążenia, skalowanie w pionie poprzez zmianę warstw obliczeniowych lub skalowanie w poziomie przy użyciu repliki odczytu.

Wszystkie metryki platformy Azure mają częstotliwość jednominutową, a każda Metryka zapewnia 30-dniową historię. Można skonfigurować alerty dotyczące metryk. Instrukcje krok po kroku znajdują się w temacie [How to set up Alerts](./how-to-alert-on-metric.md). Inne zadania obejmują Konfigurowanie zautomatyzowanych akcji, wykonywanie zaawansowanych analiz i archiwizowanie historii. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Metrics](../../azure-monitor/data-platform.md).

### <a name="list-of-metrics"></a>Lista metryk
Te metryki są dostępne dla Azure Database for MySQL:

|Nazwa wyświetlana metryki|Metric|Jednostka|Opis|
|---|---|---|---|
|Procent procesora CPU hosta|cpu_percent|Procent|Procent użycia procesora CPU na serwerze, w tym wykorzystanie procesora CPU zarówno z obciążeń klientów, jak i procesów Azure MySQL|
|Sieć hosta w |network_bytes_ingress|Bajty|Przychodzący ruch sieciowy na serwerze, łącznie z ruchem zarówno z bazy danych klienta, jak i funkcji Azure MySQL, takich jak replikacja, monitorowanie, dzienniki itp.|
|Ruch wychodzący w sieci hosta|network_bytes_egress|Bajty|Wychodzący ruch sieciowy na serwerze, łącznie z ruchem zarówno z bazy danych klienta, jak i funkcji Azure MySQL, takich jak replikacja, monitorowanie, dzienniki itp.|
|Zwłoka replikacji|replication_lag|Sekundy|Czas od ostatniej odtworzonej transakcji. Ta Metryka jest dostępna tylko dla serwerów repliki.|
|Aktywne połączenia|active_connection|Liczba|Liczba aktywnych połączeń z serwerem.|
|Używany magazyn kopii zapasowych|backup_storage_used|Bajty|Ilość używanego magazynu kopii zapasowych.|
|Procent operacji we/wy|io_consumption_percent|Procent|Procent operacji we/wy w użyciu.|
|Procent pamięci hosta|memory_percent|Procent|Wartość procentowa ilości pamięci używanej na serwerze, w tym użycie pamięci zarówno z obciążenia klienta, jak i procesów Azure MySQL|
|Limit magazynu|storage_limit|Bajty|Maksymalny magazyn dla tego serwera.|
|Procent magazynu|storage_percent|Procent|Wartość procentowa używanej przestrzeni dyskowej poza maksymalną.|
|Używany magazyn|storage_used|Bajty|Ilość używanej pamięci masowej. Magazyn używany przez usługę może obejmować pliki bazy danych, dzienniki transakcji i Dzienniki serwera.|
|Łączna liczba połączeń|total_connections|Liczba|Łączna liczba połączeń z serwerem|
|Przerwane połączenia|aborted_connections|Liczba|Liczba nieudanych prób nawiązania połączenia z bazą danych MySQL, na przykład niepowodzenia połączenia z powodu nieprawidłowych poświadczeń.|
|Zapytania|wybiera|Liczba|Liczba zapytań na sekundę|

## <a name="next-steps"></a>Następne kroki
- Zobacz [jak skonfigurować alerty](./how-to-alert-on-metric.md) , aby uzyskać wskazówki dotyczące tworzenia alertu dotyczącego metryki.
- Dowiedz się więcej o [skalowaniu IOPS](./concepts/../concepts-compute-storage.md#iops) w celu zwiększenia wydajności.