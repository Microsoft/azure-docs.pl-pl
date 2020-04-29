---
title: Odczytaj repliki — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano funkcję Read Replica w Azure Database for PostgreSQL — pojedynczy serwer.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 545d04bdede76a6ce25c9e4665f39c01ff6caa73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81531987"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Odczytaj repliki w Azure Database for PostgreSQL — pojedynczy serwer

Funkcja Read Replica umożliwia replikowanie danych z serwera Azure Database for PostgreSQL do serwera tylko do odczytu. Z serwera głównego można replikować maksymalnie pięć replik. Repliki są aktualizowane asynchronicznie z technologią natywnej replikacji aparatu PostgreSQL.

Repliki to nowe serwery, którymi można zarządzać podobnie jak regularne Azure Database for PostgreSQL serwery. Dla każdej repliki odczytu są naliczane opłaty za zasoby obliczeniowe rdzeni wirtualnych i magazyn w GB/miesiąc.

Dowiedz się [, jak tworzyć repliki i zarządzać nimi](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Kiedy używać repliki odczytu
Funkcja odczytu repliki pomaga zwiększyć wydajność i skalowalność obciążeń intensywnie korzystających z odczytu. Obciążenia odczytu mogą być odizolowane dla replik, podczas gdy obciążenia zapisu mogą być kierowane do wzorca.

Typowy scenariusz polega na tym, że obciążenia analizy biznesowej i analizy używają repliki odczytu jako źródła danych do raportowania.

Ponieważ repliki są tylko do odczytu, nie zmniejszają bezpośrednio obciążeń związanych z pojemnością zapisu na serwerze głównym. Ta funkcja nie jest przeznaczona dla obciążeń intensywnie korzystających z pisania.

Funkcja Read Replica używa replikacji asynchronicznej PostgreSQL. Ta funkcja nie jest przeznaczona do scenariuszy replikacji synchronicznej. Nastąpi wymierne opóźnienie między serwerem głównym a repliką. Dane z repliki ostatecznie staną się spójne z danymi na serwerze głównym. Użyj tej funkcji dla obciążeń, które mogą obsłużyć to opóźnienie.

## <a name="cross-region-replication"></a>Replikacja między regionami
Replikę odczytu można utworzyć w innym regionie niż serwer główny. Replikacja między regionami może być przydatna w scenariuszach takich jak planowanie odzyskiwania po awarii lub umieszczenie danych bliżej użytkowników.

Serwer główny może być w dowolnym [regionie Azure Database for PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Serwer główny może mieć replikę w osobnym regionie lub regionach uniwersalnej repliki. Na poniższej ilustracji przedstawiono, które regiony replik są dostępne w zależności od regionu głównego.

[![Odczytaj regiony repliki](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiony uniwersalnej repliki
Można zawsze utworzyć replikę odczytu w jednym z następujących regionów, niezależnie od tego, gdzie znajduje się serwer główny. Są to uniwersalne regiony repliki:

Australia Wschodnia, Australia Południowo-Wschodnia, środkowe stany USA, Azja Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowo-Wschodnia, Płn. Północno-środkowe stany USA, Europa Północna, Południowe stany USA, Azja Południowo-Wschodnia, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Europa Zachodnia i zachodnie stany USA.

* Zachodnie stany USA 2 są tymczasowo niedostępne jako lokalizacja repliki między regionami.


### <a name="paired-regions"></a>Sparowane regiony
Oprócz regionów uniwersalnej repliki można utworzyć replikę odczytu w sparowanym regionie platformy Azure serwera głównego. Jeśli nie znasz pary regionów, możesz dowiedzieć się więcej z [artykułu z sparowanymi regionami platformy Azure](../best-practices-availability-paired-regions.md).

Jeśli używasz replik między regionami do planowania odzyskiwania po awarii, zalecamy utworzenie repliki w sparowanym regionie, a nie w jednym z innych regionów. Sparowane regiony umożliwiają uniknięcie jednoczesnych aktualizacji i określanie priorytetów fizycznej izolacji i miejsca zamieszkania danych.  

Istnieją ograniczenia, które należy wziąć pod uwagę: 

* Dostępność regionalna: Azure Database for PostgreSQL jest dostępna w regionie zachodnie stany USA 2, Francja środkowa, Zjednoczone Emiraty Arabskie i Niemcy środkowe. Jednak ich sparowane regiony nie są dostępne.
    
* Pary jednokierunkowe: niektóre regiony platformy Azure są sparowane tylko w jednym kierunku. Regiony te obejmują Indie Zachodnie, Brazylia Południowa. 
   Oznacza to, że serwer główny w regionie zachodnie Indie może utworzyć replikę w Indiach Południowej. Jednak główny serwer nie może utworzyć repliki w Indiach zachodnim. Jest to spowodowane tym, że region pomocniczy w zachodniej Indiach to Indie Południowe, ale region pomocniczy w Republice Południowej Indie nie jest Indie Zachodnie.


## <a name="create-a-replica"></a>Tworzenie repliki
Po uruchomieniu przepływu pracy tworzenia repliki zostanie utworzony pusty serwer Azure Database for PostgreSQL. Nowy serwer jest wypełniony danymi znajdującymi się na serwerze głównym. Czas utworzenia zależy od ilości danych na serwerze głównym oraz czasu od ostatniego cotygodniowej pełnej kopii zapasowej. Czas może się wahać od kilku minut do kilku godzin.

Każda replika ma włączoną funkcję [autowzrostu](concepts-pricing-tiers.md#storage-auto-grow)magazynu. Funkcja autozwiększania umożliwia replikom przechowywanie danych replikowanych do nich i uniemożliwia przerwanie replikacji spowodowane błędami magazynowania.

Funkcja Read Replica używa replikacji fizycznej PostgreSQL, a nie replikacji logicznej. Replikacja strumieniowa przy użyciu miejsc replikacji jest domyślnym trybem operacji. W razie potrzeby wysyłanie dziennika służy do wykrycia.

Dowiedz się [, jak utworzyć replikę odczytu w Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Nawiązywanie połączenia z repliką
Podczas tworzenia repliki nie są dziedziczone reguły zapory ani punkt końcowy usługi sieci wirtualnej serwera głównego. Te reguły należy skonfigurować niezależnie dla repliki.

Replika dziedziczy konto administratora z serwera głównego. Wszystkie konta użytkowników na serwerze głównym są replikowane do replik odczytu. Można nawiązać połączenie z repliką odczytu tylko przy użyciu kont użytkowników, które są dostępne na serwerze głównym.

Możesz połączyć się z repliką przy użyciu nazwy hosta i prawidłowego konta użytkownika, tak jak w przypadku zwykłego serwera Azure Database for PostgreSQL. W przypadku serwera o nazwie **moja replika** z **nazwą administratora administrator można**nawiązać połączenie z repliką przy użyciu PSQL:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

W wierszu polecenia wprowadź hasło dla konta użytkownika.

## <a name="monitor-replication"></a>Monitorowanie replikacji
Azure Database for PostgreSQL udostępnia dwie metryki do monitorowania replikacji. Dwie metryki to **maksymalne opóźnienie między replikami** i **zwłoką repliki**. Aby dowiedzieć się, jak wyświetlić te metryki, zobacz sekcję **monitorowanie repliki** w [artykule Jak uzyskać informacje o replikacji](howto-read-replicas-portal.md).

Metryka **maks. opóźnienie między replikami** pokazuje opóźnienie w bajtach między wzorcem a repliką najbardziej opóźnioną. Ta Metryka jest dostępna tylko na serwerze głównym.

Metryka **opóźnienia repliki** przedstawia czas od ostatniego odtworzonej transakcji. Jeśli na serwerze głównym nie ma żadnych transakcji, Metryka uwzględnia ten czas opóźnienia. Ta Metryka jest dostępna tylko dla serwerów repliki. Opóźnienie repliki jest obliczane na podstawie `pg_stat_wal_receiver` widoku:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Ustaw Alert, aby poinformować Cię, gdy zwłoka repliki osiągnie wartość, która nie jest akceptowalna dla obciążenia. 

Aby uzyskać dodatkowe informacje, wykonaj bezpośrednie zapytanie do serwera głównego, aby uzyskać opóźnienie replikacji w bajtach we wszystkich replikach.

W programie PostgreSQL w wersji 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

W programie PostgreSQL w wersji 9,6 i starszej:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Jeśli serwer główny lub replika do odczytu zostanie ponownie uruchomiony, czas potrzebny do ponownego uruchomienia i przechwycenia zostanie odzwierciedlony w metryce zwłoki repliki.

## <a name="stop-replication"></a>Zatrzymywanie replikacji
Można zatrzymać replikację między serwerem głównym a repliką. Akcja zatrzymania powoduje ponowne uruchomienie repliki i usunięcie jej ustawień replikacji. Po zatrzymaniu replikacji między serwerem głównym a repliką odczytu replika stanie się serwerem autonomicznym. Dane na serwerze autonomicznym to dane, które były dostępne w replice w momencie uruchomienia polecenia Zatrzymaj replikację. Serwer autonomiczny nie jest przechwytywany z serwerem głównym.

> [!IMPORTANT]
> Serwer autonomiczny nie może zostać ponownie utworzony w replice.
> Przed zatrzymaniem replikacji w replice odczytu upewnij się, że replika ma wszystkie wymagane dane.

Gdy zatrzymasz replikację, replika utraci wszystkie linki do poprzednich i innych replik.

Dowiedz się, jak [zatrzymać replikację do repliki](howto-read-replicas-portal.md).

## <a name="failover"></a>Tryb failover
Nie ma automatycznej pracy awaryjnej między serwerami Master i replikami. 

Ponieważ replikacja jest asynchroniczna, między wzorcem a repliką jest zwłoka. Na czas opóźnienia może wpływać wiele czynników, takich jak zmniejszanie obciążenia uruchomionego na serwerze głównym oraz opóźnienia między centrami danych. W większości przypadków zwłoki repliki od kilku sekund do kilku minut. Rzeczywiste opóźnienie replikacji można śledzić przy użyciu *opóźnienia repliki*metryk, które jest dostępne dla każdej repliki. Ta Metryka przedstawia czas od ostatniego odtworzonej transakcji. Zalecamy, aby określić, co to jest średnie opóźnienie, obserwując opóźnienie repliki w danym okresie czasu. Można ustawić alert w przypadku zwłoki repliki, aby w przypadku, gdy znajdzie się poza oczekiwanym zakresem, można wykonać akcję.

> [!Tip]
> W przypadku przejścia w tryb failover do repliki zwłoka w momencie odłączenia repliki od wzorca będzie wskazywać, ile danych jest utraconych.

Po podjęciu decyzji o przejściu do trybu failover w replice 

1. Zatrzymaj replikację do repliki<br/>
   Ten krok jest niezbędny, aby serwer repliki mógł akceptować operacje zapisu. W ramach tego procesu serwer repliki zostanie ponownie uruchomiony i zostanie odłączona od wzorca. Po zainicjowaniu zatrzymania replikacji proces zaplecza zwykle trwa około 2 minuty. Zapoznaj się z sekcją [Zatrzymaj replikację](#stop-replication) tego artykułu, aby poznać konsekwencje tej akcji.
    
2. Wskazywanie aplikacji na (dawniej) replikę<br/>
   Każdy serwer ma unikatowe parametry połączenia. Zaktualizuj swoją aplikację, tak aby wskazywała replikę (dawniej), a nie główną.
    
Po pomyślnym przetworzeniu odczytów i zapisów aplikacja została ukończona w trybie failover. Czas przestoju, w jakim zależą od aplikacji, będzie zależny od tego, kiedy wykryjesz problem, i wykonaj kroki 1 i 2 powyżej.


## <a name="considerations"></a>Zagadnienia do rozważenia

Ta sekcja zawiera podsumowanie zagadnień dotyczących funkcji odczytu repliki.

### <a name="prerequisites"></a>Wymagania wstępne
Przed utworzeniem repliki odczytu, `azure.replication_support` parametr musi być ustawiony na **replikę** na serwerze głównym. Gdy ten parametr zostanie zmieniony, wymagane jest ponowne uruchomienie serwera, aby zmiany zaczęły obowiązywać. Ten `azure.replication_support` parametr ma zastosowanie tylko do warstw ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

### <a name="new-replicas"></a>Nowe repliki
Replika odczytu jest tworzona jako nowy serwer Azure Database for PostgreSQL. Nie można wykonać istniejącego serwera w replice. Nie można utworzyć repliki innej repliki odczytu.

### <a name="replica-configuration"></a>Konfiguracja repliki
Replika jest tworzona przy użyciu tych samych ustawień obliczeniowych i magazynu co główny. Po utworzeniu repliki można zmienić kilka ustawień niezależnie od serwera głównego: generowanie obliczeń, rdzeni wirtualnych, magazyn i okres przechowywania kopii zapasowych. Warstwę cenową można także zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub z niej.

> [!IMPORTANT]
> Przed zaktualizowaniem ustawień głównych do nowej wartości należy zaktualizować konfigurację repliki do wartości równej lub wyższej. Dzięki temu replika może być na bieżąco ze zmianami wprowadzonymi we wzorcu.

PostgreSQL wymaga, aby wartość `max_connections` parametru w replice odczytu była większa lub równa wartości głównej; w przeciwnym razie replika nie zostanie uruchomiona. W Azure Database for PostgreSQL wartość `max_connections` parametru jest określana na podstawie jednostki SKU. Aby uzyskać więcej informacji, zobacz [limity w Azure Database for PostgreSQL](concepts-limits.md). 

Jeśli spróbujesz zaktualizować wartości serwera opisane powyżej, ale nie przestrzegasz limitów, zostanie wyświetlony komunikat o błędzie.

Reguły zapory, reguły sieci wirtualnej i ustawienia parametrów nie są dziedziczone z serwera głównego do repliki, gdy replika zostanie utworzona lub później.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL wymaga](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) , aby wartość `max_prepared_transactions` parametru w replice odczytu była większa lub równa wartości głównej; w przeciwnym razie replika nie zostanie uruchomiona. Jeśli chcesz zmienić `max_prepared_transactions` wzorzec, najpierw zmień go na repliki.

### <a name="stopped-replicas"></a>Repliki zatrzymane
Jeśli zatrzymasz replikację między serwerem głównym a repliką odczytu, replika zostanie ponownie uruchomiona w celu zastosowania zmiany. Zatrzymana replika stanie się serwerem autonomicznym, który akceptuje zarówno operacje odczytu, jak i zapisu. Serwer autonomiczny nie może zostać ponownie utworzony w replice.

### <a name="deleted-master-and-standalone-servers"></a>Usunięto serwery główne i autonomiczne
Po usunięciu serwera głównego wszystkie jego repliki odczytu stają się serwerami autonomicznymi. Repliki są ponownie uruchamiane w celu odzwierciedlenia tej zmiany.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi w Azure Portal](howto-read-replicas-portal.md).
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi w interfejsie wiersza polecenia platformy Azure i interfejsu API REST](howto-read-replicas-cli.md).
