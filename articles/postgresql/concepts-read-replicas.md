---
title: Odczytaj repliki — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano funkcję Read Replica w Azure Database for PostgreSQL — pojedynczy serwer.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 7f81e6182209e29e41a21abadbaf05518844d201
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490173"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Odczytaj repliki w Azure Database for PostgreSQL — pojedynczy serwer

Funkcja Read Replica umożliwia replikowanie danych z serwera Azure Database for PostgreSQL do serwera tylko do odczytu. Można replikować z serwera podstawowego do maksymalnie pięciu replik. Repliki są aktualizowane asynchronicznie przy użyciu technologii natywnej replikacji aparatu PostgreSQL.

Repliki to nowe serwery, którymi można zarządzać podobnie jak zwykłymi serwerami usługi Azure Database for PostgreSQL. Dla każdej repliki odczytu są naliczane opłaty za zasoby obliczeniowe rdzeni wirtualnych i magazyn w GB/miesiąc.

Dowiedz się [, jak tworzyć repliki i zarządzać nimi](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Kiedy używać repliki odczytu
Funkcja odczytu repliki pomaga zwiększyć wydajność i skalowalność obciążeń intensywnie korzystających z odczytu. Obciążenia odczytu mogą być odizolowane dla replik, podczas gdy obciążenia zapisu mogą być kierowane do podstawowego.

Typowy scenariusz polega na tym, że obciążenia analizy biznesowej i analizy używają repliki odczytu jako źródła danych do raportowania.

Ponieważ repliki są tylko do odczytu, nie zmniejszają bezpośrednio obciążeń związanych z pojemnością zapisu w podstawowym. Ta funkcja nie jest przeznaczona dla obciążeń intensywnie korzystających z zapisu.

Funkcja Read Replica używa replikacji asynchronicznej PostgreSQL. Ta funkcja nie jest przeznaczona do scenariuszy replikacji synchronicznej. Nastąpi wymierne opóźnienie między podstawową i repliką. Dane z repliki ostatecznie staną się spójne z danymi na serwerze podstawowym. Użyj tej funkcji dla obciążeń, które mogą obsłużyć to opóźnienie.

## <a name="cross-region-replication"></a>Replikacja między regionami
Replikę odczytu można utworzyć w innym regionie niż na serwerze podstawowym. Replikacja między regionami może być przydatna w scenariuszach takich jak planowanie odzyskiwania po awarii lub umieszczenie danych bliżej użytkowników.

>[!NOTE]
> Serwery warstwy Podstawowa obsługują tylko replikację tego samego regionu.

Serwer podstawowy może być w dowolnym [regionie Azure Database for PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Serwer podstawowy może mieć replikę w osobnym regionie lub regionach uniwersalnej repliki. Na poniższej ilustracji przedstawiono, które regiony replik są dostępne w zależności od regionu podstawowego.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Odczytaj regiony repliki":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiony uniwersalnej repliki
Można zawsze utworzyć replikę odczytu w jednym z następujących regionów, niezależnie od tego, gdzie znajduje się serwer podstawowy. Są to uniwersalne regiony repliki:

Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada środkowa, Kanada Wschodnia, środkowe stany USA, Azja Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowa, Północno-środkowe stany USA, Europa Północna, Południowo-środkowe stany USA, Zachodnie Zjednoczone Królestwo Południowe Zjednoczone Królestwo Azja Południowo-Wschodnia.

### <a name="paired-regions"></a>Sparowane regiony
Oprócz regionów uniwersalnej repliki można utworzyć replikę odczytu w sparowanym regionie platformy Azure na serwerze podstawowym. Jeśli nie znasz pary regionów, możesz dowiedzieć się więcej z [artykułu z sparowanymi regionami platformy Azure](../best-practices-availability-paired-regions.md).

Jeśli używasz replik między regionami do planowania odzyskiwania po awarii, zalecamy utworzenie repliki w sparowanym regionie, a nie w jednym z innych regionów. Sparowane regiony umożliwiają uniknięcie jednoczesnych aktualizacji i określanie priorytetów fizycznej izolacji i miejsca zamieszkania danych.  

Istnieją ograniczenia, które należy wziąć pod uwagę: 

* Dostępność regionalna: Azure Database for PostgreSQL jest dostępna w regionach Francja środkowa, Zjednoczone Emiraty Arabskie i Niemcy środkowe. Jednak ich sparowane regiony nie są dostępne.
    
* Pary jednokierunkowe: niektóre regiony platformy Azure są sparowane tylko w jednym kierunku. Regiony te obejmują Indie Zachodnie, Brazylia Południowa. 
   Oznacza to, że serwer podstawowy w języku zachodnie Indie może utworzyć replikę w Indiach Południowej. Jednak serwer podstawowy w Republice Południowej Indie nie może utworzyć repliki w Indiach zachodnim. Jest to spowodowane tym, że region pomocniczy w zachodniej Indiach to Indie Południowe, ale region pomocniczy w Republice Południowej Indie nie jest Indie Zachodnie.


## <a name="create-a-replica"></a>Tworzenie repliki
Po uruchomieniu przepływu pracy tworzenia repliki zostanie utworzony pusty serwer Azure Database for PostgreSQL. Nowy serwer jest wypełniony danymi znajdującymi się na serwerze podstawowym. Czas utworzenia zależy od ilości danych w podstawowej i czasu od ostatniego cotygodniowej pełnej kopii zapasowej. Czas może się wahać od kilku minut do kilku godzin.

Każda replika ma włączoną funkcję [autowzrostu](concepts-pricing-tiers.md#storage-auto-grow)magazynu. Funkcja autozwiększania umożliwia replikom przechowywanie danych replikowanych do nich i uniemożliwia przerwanie replikacji spowodowane błędami magazynowania.

Funkcja Read Replica używa replikacji fizycznej PostgreSQL, a nie replikacji logicznej. Replikacja strumieniowa przy użyciu miejsc replikacji jest domyślnym trybem operacji. W razie potrzeby wysyłanie dziennika służy do wykrycia.

Dowiedz się [, jak utworzyć replikę odczytu w Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Nawiązywanie połączenia z repliką
Podczas tworzenia repliki nie są dziedziczone reguły zapory ani punkt końcowy usługi sieci wirtualnej na serwerze podstawowym. Te reguły należy skonfigurować niezależnie dla repliki.

Replika dziedziczy konto administratora z serwera podstawowego. Wszystkie konta użytkowników na serwerze podstawowym są replikowane do replik odczytu. Można nawiązać połączenie z repliką odczytu tylko przy użyciu kont użytkowników, które są dostępne na serwerze podstawowym.

Możesz połączyć się z repliką przy użyciu nazwy hosta i prawidłowego konta użytkownika, tak jak w przypadku zwykłego serwera Azure Database for PostgreSQL. W przypadku serwera o nazwie **moja replika** z **nazwą administratora administrator można**nawiązać połączenie z repliką przy użyciu PSQL:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

W wierszu polecenia wprowadź hasło dla konta użytkownika.

## <a name="monitor-replication"></a>Monitorowanie replikacji
Azure Database for PostgreSQL udostępnia dwie metryki do monitorowania replikacji. Dwie metryki to **maksymalne opóźnienie między replikami** i **zwłoką repliki**. Aby dowiedzieć się, jak wyświetlić te metryki, zobacz sekcję **monitorowanie repliki** w [artykule Jak uzyskać informacje o replikacji](howto-read-replicas-portal.md).

Metryka **maks. opóźnienie między replikami** pokazuje opóźnienie w bajtach między podstawową i najbardziej opóźnioną repliką. Ta Metryka jest dostępna tylko na serwerze podstawowym i będzie dostępna tylko wtedy, gdy co najmniej jedna z replik odczytu jest połączona z serwerem podstawowym.

Metryka **opóźnienia repliki** przedstawia czas od ostatniego odtworzonej transakcji. Jeśli na serwerze podstawowym nie ma żadnych transakcji, Metryka uwzględnia ten czas opóźnienia. Ta Metryka jest dostępna tylko dla serwerów repliki. Opóźnienie repliki jest obliczane na podstawie `pg_stat_wal_receiver` widoku:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Ustaw Alert, aby poinformować Cię, gdy zwłoka repliki osiągnie wartość, która nie jest akceptowalna dla obciążenia. 

Aby uzyskać dodatkowe informacje, zbadaj serwer podstawowy bezpośrednio, aby uzyskać zwłokę replikacji w bajtach we wszystkich replikach.

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
> Jeśli serwer podstawowy lub replika do odczytu zostanie ponownie uruchomiony, czas potrzebny do ponownego uruchomienia i przechwycenia zostanie odzwierciedlony w metryce zwłoki repliki.

## <a name="stop-replication"></a>Zatrzymywanie replikacji
Można zatrzymać replikację między podstawową i repliką. Akcja zatrzymania powoduje ponowne uruchomienie repliki i usunięcie jej ustawień replikacji. Po zatrzymaniu replikacji między serwerem podstawowym a repliką odczytu replika stanie się serwerem autonomicznym. Dane na serwerze autonomicznym to dane, które były dostępne w replice w momencie uruchomienia polecenia Zatrzymaj replikację. Serwer autonomiczny nie jest przechwytywany z serwerem podstawowym.

> [!IMPORTANT]
> Serwer autonomiczny nie może zostać ponownie utworzony w replice.
> Przed zatrzymaniem replikacji w replice odczytu upewnij się, że replika ma wszystkie wymagane dane.

Gdy zatrzymasz replikację, replika utraci wszystkie linki do poprzednich, głównych i innych replik.

Dowiedz się, jak [zatrzymać replikację do repliki](howto-read-replicas-portal.md).

## <a name="failover"></a>Tryb failover
Nie ma automatycznej pracy awaryjnej między serwerami podstawowymi i repliki. 

Ponieważ replikacja jest asynchroniczna, między podstawową i repliką występuje opóźnienie. Na czas opóźnienia może wpływać wiele czynników, takich jak zmniejszanie obciążenia uruchomionego na serwerze podstawowym oraz opóźnienia między centrami danych. W typowych przypadkach opóźnienia repliki są w zakresie od kilku sekund do kilku minut. Jednak w przypadkach, gdy podstawowy przebiega bardzo duże obciążenia, a replika nie jest wystarczająco szybko przechwycenia, opóźnienie może być wyższe. Rzeczywiste opóźnienie replikacji można śledzić przy użyciu *opóźnienia repliki*metryk, które jest dostępne dla każdej repliki. Ta Metryka przedstawia czas od ostatniego odtworzonej transakcji. Zalecamy, aby określić, co to jest średnie opóźnienie, obserwując opóźnienie repliki w danym okresie czasu. Można ustawić alert w przypadku zwłoki repliki, aby w przypadku, gdy znajdzie się poza oczekiwanym zakresem, można wykonać akcję.

> [!Tip]
> W przypadku przejścia w tryb failover do repliki zwłoka w momencie odłączenia repliki od podstawowego będzie wskazywać, ile danych jest utraconych.

Po podjęciu decyzji o przejściu do trybu failover w replice 

1. Zatrzymaj replikację do repliki<br/>
   Ten krok jest niezbędny, aby serwer repliki mógł akceptować operacje zapisu. W ramach tego procesu serwer repliki zostanie ponownie uruchomiony i zostanie odłączona od podstawowego. Po zainicjowaniu zatrzymania replikacji proces zaplecza zwykle trwa około 2 minuty. Zapoznaj się z sekcją [Zatrzymaj replikację](#stop-replication) tego artykułu, aby poznać konsekwencje tej akcji.
    
2. Wskazywanie aplikacji na (dawniej) replikę<br/>
   Każdy serwer ma unikatowe parametry połączenia. Zaktualizuj swoją aplikację, tak aby wskazywała replikę (dawniej) zamiast podstawowej.
    
Po pomyślnym przetworzeniu odczytów i zapisów aplikacja została ukończona w trybie failover. Czas przestoju, w jakim zależą od aplikacji, będzie zależny od tego, kiedy wykryjesz problem, i wykonaj kroki 1 i 2 powyżej.

### <a name="disaster-recovery"></a>Odzyskiwanie po awarii

W przypadku wystąpienia poważnych zdarzeń awaryjnych, takich jak strefa dostępności lub awarie regionalne, można wykonać operację odzyskiwania po awarii, promując replikę odczytu. W portalu interfejsu użytkownika można przejść do serwera odczytywania repliki. Następnie kliknij kartę replikacja, a następnie Zatrzymaj replikę, aby zapewnić jej niezależny serwer. Alternatywnie można użyć [interfejsu wiersza polecenia platformy Azure](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop) do zatrzymania i podniesienia poziomu serwera repliki.

## <a name="considerations"></a>Zagadnienia do rozważenia

Ta sekcja zawiera podsumowanie zagadnień dotyczących funkcji odczytu repliki.

### <a name="prerequisites"></a>Wymagania wstępne
Odczytywanie replik i [dekodowanie logiczne](concepts-logical.md) są zależne od dziennika Postgres zapisu (WAL) w celu uzyskania informacji. Te dwie funkcje wymagają różnych poziomów rejestrowania z Postgres. Dekodowanie logiczne wymaga wyższego poziomu rejestrowania niż repliki odczytu.

Aby skonfigurować odpowiedni poziom rejestrowania, użyj parametru Obsługa replikacji platformy Azure. Obsługa replikacji platformy Azure ma trzy opcje ustawień:

* **Wyłączone** — umieszcza najniższe informacje w Wal. To ustawienie nie jest dostępne na większości serwerów Azure Database for PostgreSQL.  
* **Replika** — większa niż **wyłączona**. Jest to minimalny poziom rejestrowania, który jest wymagany do działania [replik odczytu](concepts-read-replicas.md) . To ustawienie jest domyślne na większości serwerów.
* **Logiczne** — więcej informacji niż **replika**. Jest to minimalny poziom rejestrowania kodu logicznego do pracy. Odczytaj repliki również działają w tym ustawieniu.

Po zmianie tego parametru należy ponownie uruchomić serwer. Wewnętrznie, ten parametr ustawia parametry Postgres `wal_level` , `max_replication_slots` i `max_wal_senders` .

### <a name="new-replicas"></a>Nowe repliki
Replika odczytu jest tworzona jako nowy serwer Azure Database for PostgreSQL. Nie można wykonać istniejącego serwera w replice. Nie można utworzyć repliki innej repliki odczytu.

### <a name="replica-configuration"></a>Konfiguracja repliki
Replika jest tworzona przy użyciu tych samych ustawień obliczeniowych i magazynu co podstawowy. Po utworzeniu repliki można zmienić kilka ustawień, w tym okres przechowywania magazynu i kopii zapasowych.

Reguły zapory, reguły sieci wirtualnej i ustawienia parametrów nie są dziedziczone z serwera podstawowego do repliki, gdy replika zostanie utworzona lub później.

### <a name="scaling"></a>Skalowanie
Skalowanie rdzeni wirtualnych lub między Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci:
* PostgreSQL wymaga `max_connections` , aby ustawienie na serwerze pomocniczym było [większe lub równe ustawieniu na podstawowym](https://www.postgresql.org/docs/current/hot-standby.html), w przeciwnym razie nie zostanie uruchomiony pomocniczy.
* W Azure Database for PostgreSQL maksymalne dozwolone połączenia dla każdego serwera są stałe dla jednostki SKU obliczeń, ponieważ połączenia zajmują pamięć. Możesz dowiedzieć się więcej o [mapowaniu między max_connections i obliczeniowymi](concepts-limits.md)jednostkami SKU.
* **Skalowanie w górę**: najpierw Skaluj w górę obliczenia repliki, a następnie Skaluj w górę. Ta kolejność uniemożliwi występowaniu błędów naruszających `max_connections` wymaganie.
* **Skalowanie w dół**: najpierw Skaluj w dół podstawowe wartości obliczeniowe, a następnie Skaluj w dół replikę. Jeśli spróbujesz skalować replikę niższą niż podstawowa, wystąpi błąd, ponieważ narusza to `max_connections` wymaganie.

Skalowanie magazynu:
* Wszystkie repliki mają włączoną funkcję autozwiększania rozmiaru magazynu, aby zapobiec problemom z replikacją z repliki pełnej magazynu. Nie można wyłączyć tego ustawienia.
* Możesz również ręcznie skalować w górę magazyn, tak jak w przypadku dowolnego innego serwera


### <a name="basic-tier"></a>Warstwa Podstawowa
Serwery warstwy Podstawowa obsługują tylko replikację tego samego regionu.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL wymaga](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) , `max_prepared_transactions` aby wartość parametru w replice odczytu była większa lub równa wartości podstawowej. w przeciwnym razie replika nie zostanie uruchomiona. Aby zmienić `max_prepared_transactions` podstawową, należy najpierw zmienić ją na replikach.

### <a name="stopped-replicas"></a>Repliki zatrzymane
Jeśli zatrzymasz replikację między serwerem podstawowym a repliką odczytu, replika zostanie ponownie uruchomiona w celu zastosowania zmiany. Zatrzymana replika stanie się serwerem autonomicznym, który akceptuje zarówno operacje odczytu, jak i zapisu. Serwer autonomiczny nie może zostać ponownie utworzony w replice.

### <a name="deleted-primary-and-standalone-servers"></a>Usunięto serwery podstawowe i autonomiczne
Po usunięciu serwera podstawowego wszystkie jego repliki odczytu stają się serwerami autonomicznymi. Repliki są ponownie uruchamiane w celu odzwierciedlenia tej zmiany.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi w Azure Portal](howto-read-replicas-portal.md).
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi w interfejsie wiersza polecenia platformy Azure i interfejsu API REST](howto-read-replicas-cli.md).