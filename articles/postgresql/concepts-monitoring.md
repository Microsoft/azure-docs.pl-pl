---
title: Monitorowanie i dostrajanie-Azure Database for PostgreSQL-pojedynczy serwer
description: W tym artykule opisano funkcje monitorowania i dostrajania w Azure Database for PostgreSQL-pojedynczym serwerze.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 4fd16e9dcf9f0b75b48311adf3e9282adbce2a25
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708717"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Monitorowanie i dostrajanie Azure Database for PostgreSQL-pojedynczego serwera
Monitorowanie danych dotyczących serwerów ułatwia rozwiązywanie problemów i optymalizację w obciążeniu. Azure Database for PostgreSQL oferuje różne opcje monitorowania w celu zapewnienia wglądu w zachowanie serwera programu.

## <a name="metrics"></a>Metryki
Azure Database for PostgreSQL oferuje różne metryki, które dają wgląd w zachowanie zasobów obsługujących serwer PostgreSQL. Każda Metryka jest emitowana z częstotliwością jednej minuty i ma do [93 dni historii](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#retention-of-metrics). Można skonfigurować alerty dotyczące metryk. Aby uzyskać wskazówki krok po kroku, zobacz [jak skonfigurować alerty](howto-alert-on-metric.md). Inne zadania obejmują Konfigurowanie zautomatyzowanych akcji, wykonywanie zaawansowanych analiz i archiwizowanie historii. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista metryk
Te metryki są dostępne dla Azure Database for PostgreSQL:

|Metryka|Nazwa wyświetlana metryki|Jednostka|Opis|
|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Procent|Procent użycia procesora CPU.|
|memory_percent|Procent pamięci|Procent|Procent używanej pamięci.|
|io_consumption_percent|Procent operacji we/wy|Procent|Procent operacji we/wy w użyciu. (Nie dotyczy serwerów warstwy Podstawowa).|
|storage_percent|Procent miejsca do magazynowania|Procent|Wartość procentowa używanej przestrzeni dyskowej poza maksymalną.|
|storage_used|Używany magazyn|Bajty|Ilość używanej pamięci masowej. Magazyn używany przez usługę może obejmować pliki bazy danych, dzienniki transakcji i Dzienniki serwera.|
|storage_limit|Limit magazynu|Bajty|Maksymalny magazyn dla tego serwera.|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Procent|Procent magazynu dzienników serwera używany poza maksymalnym magazynem dzienników serwera.|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Ilość używanego magazynu dzienników serwera.|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Bajty|Maksymalny magazyn dzienników serwera dla tego serwera.|
|active_connections|Aktywne połączenia|Liczba|Liczba aktywnych połączeń z serwerem.|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Liczba ustanowionych połączeń, które zakończyły się niepowodzeniem.|
|network_bytes_egress|Sieć — wyjście|Bajty|Nawiązywanie połączeń sieciowych między aktywnymi połączeniami.|
|network_bytes_ingress|Sieć — wejście|Bajty|Sieć w ramach aktywnych połączeń.|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Ilość używanego magazynu kopii zapasowych. Ta Metryka przedstawia sumę magazynu zużywanego przez wszystkie pełne kopie zapasowe bazy danych, różnicowe kopie zapasowe i kopie zapasowe dzienników przechowywane na podstawie okresu przechowywania kopii zapasowej ustawionego dla serwera. Częstotliwość wykonywania kopii zapasowych to usługa zarządzana i opisana w [artykule pojęcia](concepts-backup.md). W przypadku magazynu geograficznie nadmiarowego użycie magazynu kopii zapasowych jest dwa razy większe niż magazyn lokalnie nadmiarowy.|
|pg_replica_log_delay_in_bytes|Maksymalne opóźnienie między replikami|Bajty|Zwłoka w bajtach między podstawową i najbardziej opóźnioną repliką. Ta Metryka jest dostępna tylko na serwerze podstawowym.|
|pg_replica_log_delay_in_seconds|Zwłoka repliki|Sekundy|Czas od ostatniej odtworzonej transakcji. Ta Metryka jest dostępna tylko dla serwerów repliki.|

## <a name="server-logs"></a>Dzienniki serwera
Możesz włączyć rejestrowanie na serwerze. Te dzienniki zasobów można wysłać do [dzienników Azure monitor](../azure-monitor/log-query/log-query-overview.md), Event Hubs i konta magazynu. Aby dowiedzieć się więcej o rejestrowaniu, odwiedź stronę [Dzienniki serwera](concepts-server-logs.md) .

## <a name="query-store"></a>Magazyn zapytań
[Magazyn zapytań](concepts-query-store.md) śledzi wydajność zapytań w miarę upływu czasu, w tym statystyki środowiska uruchomieniowego zapytań i zdarzenia oczekiwania. Ta funkcja utrzymuje informacje o wydajności środowiska uruchomieniowego zapytań w systemowej bazie danych o nazwie **azure_sys** pod schematem query_store. Można kontrolować zbieranie i przechowywanie danych za pośrednictwem różnych pokręteł konfiguracyjnych.

## <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań
[Szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) działa w połączeniu z magazynem zapytań, aby udostępnić wizualizacje dostępne z Azure Portal. Te wykresy umożliwiają identyfikowanie kluczowych zapytań, które mają wpływ na wydajność. Szczegółowe informacje o wydajności zapytań jest dostępny w sekcji **Pomoc techniczna i rozwiązywanie problemów** na stronie portalu Azure Database for PostgreSQL serwera.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności
Funkcja [zalecenia dotyczące wydajności](concepts-performance-recommendations.md) umożliwia zidentyfikowanie możliwości zwiększania wydajności obciążeń. Zalecenia dotyczące wydajności zapewniają zalecenia dotyczące tworzenia nowych indeksów, które mają możliwość zwiększenia wydajności obciążeń. Aby utworzyć zalecenia dotyczące indeksów, funkcja uwzględnia różne cechy bazy danych, w tym jej schemat i obciążenie zgłoszone przez magazyn zapytań. Po wdrożeniu wszelkich zaleceń dotyczących wydajności klienci powinni przetestować wydajność, aby oszacować wpływ tych zmian. 

## <a name="planned-maintenance-notification"></a>Powiadomienie o planowanej konserwacji

**Powiadomienia o planowanej konserwacji** umożliwiają otrzymywanie alertów dotyczących nadchodzącej planowanej konserwacji na serwer Azure Database for PostgreSQL-pojedynczy. Te powiadomienia są zintegrowane z zaplanowaną konserwacją [Service Health](../service-health/overview.md) i umożliwiają wyświetlanie całej zaplanowanej konserwacji subskrypcji w jednym miejscu. Pomaga również skalować powiadomienie do odpowiednich odbiorców dla różnych grup zasobów, ponieważ użytkownik może mieć różne kontakty odpowiedzialne za różne zasoby. Otrzymasz powiadomienie o nadchodzącej konserwacji 72 godzin przed wydarzeniem.

> [!Note]
> Firma Microsoft podejmie próbę udostępnienia **powiadomienia o planowanej konserwacji** 72 godzin dla wszystkich zdarzeń. Jednak w przypadku poprawek krytycznych lub zabezpieczeń powiadomienia mogą być wysyłane bliżej zdarzenia lub zostać pominięte.

### <a name="to-receive-planned-maintenance-notification"></a>Aby odebrać powiadomienie o planowanej konserwacji

1. W [portalu](https://portal.azure.com)wybierz pozycję **Service Health**.
2. W sekcji **alerty** wybierz pozycję **alerty dotyczące kondycji**.
3. Wybierz pozycję **+ Dodaj alert kondycji usługi** i wypełnij pola.
4. Wypełnij pola wymagane. 
5. Wybierz **Typ zdarzenia**, wybierz pozycję **Planowana konserwacja** lub **Zaznacz wszystko**
6. W obszarze **grupy akcji** Określ, w jaki sposób chcesz otrzymywać alert (Otrzymuj wiadomość e-mail, wyzwól aplikację logiki itp.).  
7. Upewnij się, że w momencie utworzenia reguły włączania zostanie ustawiona wartość tak.
8. Wybierz pozycję **Utwórz regułę alertu** , aby zakończyć alert

Szczegółowe instrukcje dotyczące tworzenia **alertów dotyczących kondycji usługi**można znaleźć w sekcji [tworzenie alertów dziennika aktywności w powiadomieniach dotyczących usług](../service-health/alerts-activity-log-service-notifications.md).

> [!IMPORTANT]
> Powiadomienia o planowanej konserwacji są obecnie dostępne w wersji zapoznawczej we wszystkich regionach **z wyjątkiem** zachodnich Stanów Zjednoczonych

## <a name="next-steps"></a>Następne kroki
- Zobacz [jak skonfigurować alerty](howto-alert-on-metric.md) , aby uzyskać wskazówki dotyczące tworzenia alertu dotyczącego metryki.
- Aby uzyskać więcej informacji na temat uzyskiwania dostępu do metryk i eksportowania ich przy użyciu Azure Portal, interfejsu API REST, lub interfejsu wiersza polecenia, zobacz [Omówienie usługi Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Przeczytaj nasz blog, aby zapoznać się z [najlepszymi rozwiązaniami dotyczącymi monitorowania serwera](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
