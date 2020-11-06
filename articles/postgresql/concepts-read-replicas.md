---
title: Odczytaj repliki — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano funkcję Read Replica w Azure Database for PostgreSQL — pojedynczy serwer.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: 8fabf8169270c3162604b6535a6cf2fb07cd9a9d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422148"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Odczytaj repliki w Azure Database for PostgreSQL — pojedynczy serwer

Funkcja Read Replica umożliwia replikowanie danych z serwera Azure Database for PostgreSQL do serwera tylko do odczytu. Repliki są aktualizowane **asynchronicznie** za pomocą natywnej technologii replikacji fizycznej aparatu PostgreSQL. Można replikować z serwera podstawowego do maksymalnie pięciu replik.

Repliki to nowe serwery, którymi można zarządzać podobnie jak zwykłymi serwerami usługi Azure Database for PostgreSQL. Dla każdej repliki odczytu są naliczane opłaty za zasoby obliczeniowe rdzeni wirtualnych i magazyn w GB/miesiąc.

Dowiedz się [, jak tworzyć repliki i zarządzać nimi](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Kiedy używać repliki odczytu
Funkcja odczytu repliki pomaga zwiększyć wydajność i skalowalność obciążeń intensywnie korzystających z odczytu. Obciążenia odczytu mogą być odizolowane dla replik, podczas gdy obciążenia zapisu mogą być kierowane do podstawowego. Repliki odczytu można także wdrożyć w innym regionie i można je podwyższyć do serwera odczytu/zapisu w przypadku odzyskiwania po awarii.

Typowy scenariusz polega na tym, że obciążenia analizy biznesowej i analizy używają repliki odczytu jako źródła danych do raportowania.

Ponieważ repliki są tylko do odczytu, nie zmniejszają bezpośrednio obciążeń związanych z pojemnością zapisu w podstawowym.

### <a name="considerations"></a>Kwestie do rozważenia
Ta funkcja jest przeznaczona dla scenariuszy, w których zwłoka jest akceptowalna i przeznaczona do odciążania zapytań. Nie jest to przeznaczone do scenariuszy replikacji synchronicznej, w których oczekiwane jest aktualne dane repliki. Nastąpi wymierne opóźnienie między podstawową i repliką. Może to potrwać kilka minut, a nawet kilka godzin, w zależności od obciążenia i opóźnienia między podstawową i repliką. Dane z repliki ostatecznie staną się spójne z danymi na serwerze podstawowym. Użyj tej funkcji dla obciążeń, które mogą obsłużyć to opóźnienie. 

> [!NOTE]
> W przypadku większości obciążeń odczyty replik zapewniają aktualizacje w czasie niemal w czasie rzeczywistym z poziomu podstawowego. Jednak w przypadku trwałych ciężkich dużych obciążeń z dużą ilością operacji opóźnienie replikacji może nadal wzrosnąć i może nie być możliwe przechwycenie z podstawowym. Może to również zwiększyć użycie magazynu na poziomie podstawowym, ponieważ pliki WAL nie zostaną usunięte, dopóki nie zostaną odebrane w replice. Jeśli ta sytuacja będzie się utrzymywała, usunięcie i ponowne utworzenie repliki odczytu po zakończeniu obciążeń z intensywnym zapisem jest opcją, aby przywrócić replikę do dobrego stanu w odniesieniu do opóźnienia.
> Asynchroniczne odczyty replik nie są odpowiednie dla takich dużych obciążeń zapisu. Podczas oceniania replik odczytu dla aplikacji Monitoruj zwłokę w replice, aby uzyskać pełny cykl obciążeniowy aplikacji przez czas szczytowego i nieszczytu w celu uzyskania dostępu do możliwego opóźnienia i oczekiwanego RTO/celu punktu odzyskiwania w różnych punktach cyklu obciążenia.
> 
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

Możesz połączyć się z repliką przy użyciu nazwy hosta i prawidłowego konta użytkownika, tak jak w przypadku zwykłego serwera Azure Database for PostgreSQL. W przypadku serwera o nazwie **moja replika** z **nazwą administratora administrator można** nawiązać połączenie z repliką przy użyciu PSQL:

```bash
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

W wierszu polecenia wprowadź hasło dla konta użytkownika.

## <a name="monitor-replication"></a>Monitorowanie replikacji
Azure Database for PostgreSQL udostępnia dwie metryki do monitorowania replikacji. Dwie metryki to **maksymalne opóźnienie między replikami** i **zwłoką repliki**. Aby dowiedzieć się, jak wyświetlić te metryki, zobacz sekcję **monitorowanie repliki** w [artykule Jak uzyskać informacje o replikacji](howto-read-replicas-portal.md).

Metryka **maks. opóźnienie między replikami** pokazuje opóźnienie w bajtach między podstawową i najbardziej opóźnioną repliką. Ta Metryka ma zastosowanie i jest dostępna tylko na serwerze podstawowym i będzie dostępna tylko wtedy, gdy co najmniej jedna z replik odczytu jest połączona z lokacją podstawową, a podstawowa jest w trybie replikacji strumieniowej. Informacje o zwłoki nie wyświetlają szczegółów, gdy replika jest w procesie przechwytywania podstawowego przy użyciu zarchiwizowanych dzienników podstawowego w trybie replikacji plików.

Metryka **opóźnienia repliki** przedstawia czas od ostatniego odtworzonej transakcji. Jeśli na serwerze podstawowym nie ma żadnych transakcji, Metryka uwzględnia ten czas opóźnienia. Ta Metryka ma zastosowanie i jest dostępna tylko dla serwerów repliki. Opóźnienie repliki jest obliczane na podstawie `pg_stat_wal_receiver` widoku:

```SQL
SELECT EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Ustaw Alert, aby poinformować Cię, gdy zwłoka repliki osiągnie wartość, która nie jest akceptowalna dla obciążenia. 

Aby uzyskać dodatkowe informacje, zbadaj serwer podstawowy bezpośrednio, aby uzyskać zwłokę replikacji w bajtach we wszystkich replikach.

> [!NOTE]
> Jeśli serwer podstawowy lub replika do odczytu zostanie ponownie uruchomiony, czas potrzebny do ponownego uruchomienia i przechwycenia zostanie odzwierciedlony w metryce zwłoki repliki.

## <a name="stop-replication--promote-replica"></a>Zatrzymywanie replikacji/podwyższanie poziomu repliki
Można zatrzymać replikację między podstawową i repliką w dowolnym momencie. Akcja zatrzymania powoduje, że replika jest uruchamiana ponownie i promuje ją jako niezależny, autonomiczny serwer do odczytu i zapisu. Dane na serwerze autonomicznym to dane, które były dostępne na serwerze repliki w momencie zatrzymania replikacji. Wszystkie kolejne aktualizacje na podstawowym poziomie nie są propagowane do repliki. Jednak serwer repliki może mieć zgromadzone dzienniki, które nie zostały jeszcze zastosowane. W ramach procesu ponownego uruchamiania replika stosuje wszystkie oczekujące dzienniki przed zaakceptowaniem połączeń klientów.  

### <a name="considerations"></a>Kwestie do rozważenia
- Przed zatrzymaniem replikacji w odniesieniu do repliki odczytu Sprawdź zwłokę replikacji, aby upewnić się, że replika ma wszystkie wymagane dane. 
- Ponieważ replika odczytu ma zastosować wszystkie oczekujące dzienniki, zanim będzie można jej uczynić autonomicznym serwerem, RTO może być wyższa do zapisu dużych obciążeń, gdy replikacja kończy się nieprzerwana, ponieważ może to spowodować znaczne opóźnienie repliki. Należy zwrócić uwagę na to, aby zaplanować podwyższenie poziomu repliki.
- Nie można ponownie przetworzyć podwyższenia poziomu serwera repliki.
- W przypadku podwyższania poziomu repliki do serwera podstawowego nie można nawiązać replikacji z powrotem do starego serwera podstawowego. Jeśli chcesz wrócić do starego regionu podstawowego, możesz utworzyć nowy serwer repliki o nowej nazwie (lub) usunąć stary podstawowy i utworzyć replikę przy użyciu starej nazwy głównej.
- Jeśli masz wiele replik odczytu i jeśli podnieśsz jeden z nich jako serwer podstawowy, inne serwery repliki są nadal połączone z starym serwerem podstawowym. Może być konieczne ponowne utworzenie replik z nowego, podwyższonego serwera.

Gdy zatrzymasz replikację, replika utraci wszystkie linki do poprzednich, głównych i innych replik.

Dowiedz się, jak [zatrzymać replikację do repliki](howto-read-replicas-portal.md).

## <a name="failover-to-replica"></a>Przełączenie w tryb failover do repliki

W przypadku awarii serwera podstawowego **nie** jest automatycznie przełączona w tryb failover do repliki odczytu. 

Ponieważ replikacja jest asynchroniczna, może wystąpić duże opóźnienie między podstawową i repliką. Kwota zwłoki ma wpływ na wiele czynników, takich jak typ obciążenia uruchomionego na serwerze podstawowym i opóźnienie między serwerem podstawowym i repliką repliki. W typowych przypadkach z nominalnym obciążeniem zapisu opóźnienie repliki jest oczekiwane w ciągu kilku sekund do kilku minut. Jednak w przypadku, gdy podstawowy przebiega bardzo duże obciążenie intensywnie korzystające z zapisu, a replika nie przechwytuje się wystarczająco szybko, opóźnienie może być znacznie wyższe. Zwłokę replikacji dla każdej repliki można śledzić przy użyciu *opóźnienia repliki* metryk. Ta Metryka przedstawia czas od ostatniego odtworzenia transakcji w replice. Zalecamy określenie średniego opóźnienia przez zaobserwowanie zwłoki repliki w danym okresie czasu. Można ustawić alert dotyczący zwłoki repliki, aby w przypadku, gdy znajdzie się poza oczekiwanym zakresem, zostanie wyświetlone powiadomienie o podjęcie odpowiednich działań.

> [!Tip]
> W przypadku przejścia w tryb failover do repliki zwłoka w momencie odłączenia repliki od podstawowego będzie wskazywać, ile danych jest utraconych.

Po podjęciu decyzji o przejściu do trybu failover w replice 

1. Zatrzymaj replikację do repliki<br/>
   Ten krok jest niezbędny, aby serwer repliki stał się serwerem autonomicznym i mógł akceptować operacje zapisu. W ramach tego procesu serwer repliki zostanie ponownie uruchomiony i zostanie odłączona od podstawowego. Po zainicjowaniu zatrzymania replikacji proces zaplecza zazwyczaj trwa kilka minut, aby zastosować wszystkie pozostałe dzienniki, które nie zostały jeszcze zastosowane, i otworzyć bazę danych jako serwer z możliwością zapisu. Zapoznaj się z sekcją [Zatrzymaj replikację](#stop-replication--promote-replica) tego artykułu, aby poznać konsekwencje tej akcji.
    
2. Wskazywanie aplikacji na (dawniej) replikę<br/>
   Każdy serwer ma unikatowe parametry połączenia. Zaktualizuj parametry połączenia aplikacji w taki sposób, aby wskazywały replikę (dawniej), a nie podstawową.
    
Po pomyślnym przetworzeniu odczytów i zapisów aplikacja została ukończona w trybie failover. Czas przestoju, w jakim zależą od aplikacji, będzie zależny od tego, kiedy wykryjesz problem, i wykonaj kroki 1 i 2 powyżej.

### <a name="disaster-recovery"></a>Odzyskiwanie po awarii

W przypadku wystąpienia poważnych zdarzeń awaryjnych, takich jak strefa dostępności lub awarie regionalne, można wykonać operację odzyskiwania po awarii, promując replikę odczytu. W portalu interfejsu użytkownika można przejść do serwera odczytywania repliki. Następnie kliknij kartę replikacja, a następnie Zatrzymaj replikę, aby zapewnić jej niezależny serwer. Alternatywnie można użyć [interfejsu wiersza polecenia platformy Azure](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop) do zatrzymania i podniesienia poziomu serwera repliki.

## <a name="considerations"></a>Kwestie do rozważenia

Ta sekcja zawiera podsumowanie zagadnień dotyczących funkcji odczytu repliki.

### <a name="prerequisites"></a>Wymagania wstępne
Odczytywanie replik i [dekodowanie logiczne](concepts-logical.md) są zależne od dziennika Postgres zapisu (WAL) w celu uzyskania informacji. Te dwie funkcje wymagają różnych poziomów rejestrowania z Postgres. Dekodowanie logiczne wymaga wyższego poziomu rejestrowania niż repliki odczytu.

Aby skonfigurować odpowiedni poziom rejestrowania, użyj parametru Obsługa replikacji platformy Azure. Obsługa replikacji platformy Azure ma trzy opcje ustawień:

* **Wyłączone** — umieszcza najniższe informacje w Wal. To ustawienie nie jest dostępne na większości serwerów Azure Database for PostgreSQL.  
* **Replika** — większa niż **wyłączona**. Jest to minimalny poziom rejestrowania, który jest wymagany do działania [replik odczytu](concepts-read-replicas.md) . To ustawienie jest domyślne na większości serwerów.
* **Logiczne** — więcej informacji niż **replika**. Jest to minimalny poziom rejestrowania kodu logicznego do pracy. Odczytaj repliki również działają w tym ustawieniu.


### <a name="new-replicas"></a>Nowe repliki
Replika odczytu jest tworzona jako nowy serwer Azure Database for PostgreSQL. Nie można wykonać istniejącego serwera w replice. Nie można utworzyć repliki innej repliki odczytu.

### <a name="replica-configuration"></a>Konfiguracja repliki
Replika jest tworzona przy użyciu tych samych ustawień obliczeniowych i magazynu co podstawowy. Po utworzeniu repliki można zmienić kilka ustawień, w tym okres przechowywania magazynu i kopii zapasowych.

Reguły zapory, reguły sieci wirtualnej i ustawienia parametrów nie są dziedziczone z serwera podstawowego do repliki, gdy replika zostanie utworzona lub później.

### <a name="scaling"></a>Skalowanie
Skalowanie rdzeni wirtualnych lub między Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci:
* PostgreSQL wymaga `max_connections` , aby ustawienie na serwerze pomocniczym było [większe lub równe ustawieniu na podstawowym](https://www.postgresql.org/docs/current/hot-standby.html), w przeciwnym razie nie zostanie uruchomiony pomocniczy.
* W Azure Database for PostgreSQL maksymalne dozwolone połączenia dla każdego serwera są stałe dla jednostki SKU obliczeń, ponieważ połączenia zajmują pamięć. Możesz dowiedzieć się więcej o [mapowaniu między max_connections i obliczeniowymi](concepts-limits.md)jednostkami SKU.
* **Skalowanie w górę** : najpierw Skaluj w górę obliczenia repliki, a następnie Skaluj w górę. Ta kolejność uniemożliwi występowaniu błędów naruszających `max_connections` wymaganie.
* **Skalowanie w dół** : najpierw Skaluj w dół podstawowe wartości obliczeniowe, a następnie Skaluj w dół replikę. Jeśli spróbujesz skalować replikę niższą niż podstawowa, wystąpi błąd, ponieważ narusza to `max_connections` wymaganie.

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