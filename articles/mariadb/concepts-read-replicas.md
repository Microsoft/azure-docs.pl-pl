---
title: Odczytaj repliki — Azure Database for MariaDB
description: 'Informacje na temat odczytu replik w Azure Database for MariaDB: wybór regionów, tworzenie replik, łączenie z replikami, monitorowanie replikacji i zatrzymywanie replikacji.'
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.custom: references_regions
ms.openlocfilehash: 39547e3156a684293a0624f974a8b0930f656485
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664218"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Repliki do odczytu w usłudze Azure Database for MariaDB

Funkcja repliki do odczytu umożliwia replikowanie danych z serwera usługi Azure Database for MariaDB do serwera tylko do odczytu. Z serwera źródłowego można replikować maksymalnie pięć replik. Repliki są aktualizowane asynchronicznie przy użyciu opartej na lokalizacji pliku dziennika binarnego (binlog) aparatu MariaDB z globalnym IDENTYFIKATORem transakcji (GTID). Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji binlog](https://mariadb.com/kb/en/library/replication-overview/).

Repliki to nowe serwery, którymi można zarządzać podobnie jak regularne Azure Database for MariaDB serwery. Dla każdej repliki odczytu są naliczane opłaty za zasoby obliczeniowe rdzeni wirtualnych i magazyn w GB/miesiąc.

Więcej informacji na temat replikacji GTID można znaleźć w [dokumentacji dotyczącej replikacji MariaDB](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku _podrzędnego_, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.

## <a name="when-to-use-a-read-replica"></a>Kiedy używać repliki odczytu

Funkcja odczytu repliki pomaga zwiększyć wydajność i skalowalność obciążeń intensywnie korzystających z odczytu. Obciążenia odczytu mogą być odizolowane dla replik, podczas gdy obciążenia zapisu mogą być kierowane do podstawowego.

Typowy scenariusz polega na tym, że obciążenia analizy biznesowej i analizy używają repliki odczytu jako źródła danych do raportowania.

Ponieważ repliki są tylko do odczytu, nie zmniejszają bezpośrednio obciążeń związanych z pojemnością zapisu w podstawowym. Ta funkcja nie jest przeznaczona dla obciążeń intensywnie korzystających z zapisu.

Funkcja odczytu repliki korzysta z replikacji asynchronicznej. Ta funkcja nie jest przeznaczona do scenariuszy replikacji synchronicznej. Nastąpi wymierne opóźnienie między źródłem a repliką. Dane z repliki ostatecznie staną się spójne z danymi na serwerze podstawowym. Użyj tej funkcji dla obciążeń, które mogą obsłużyć to opóźnienie.

## <a name="cross-region-replication"></a>Replikacja między regionami

Replikę odczytu można utworzyć w innym regionie niż na serwerze źródłowym. Replikacja między regionami może być przydatna w scenariuszach takich jak planowanie odzyskiwania po awarii lub umieszczenie danych bliżej użytkowników.

Serwer źródłowy może być w dowolnym [regionie Azure Database for MariaDB](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb).  Serwer źródłowy może mieć replikę w osobnym regionie lub regionach uniwersalnej repliki. Na poniższej ilustracji przedstawiono, które regiony replik są dostępne w zależności od regionu źródłowego.

[![Odczytaj regiony repliki](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiony uniwersalnej repliki

Replikę odczytu można utworzyć w dowolnym z następujących regionów, niezależnie od lokalizacji serwera źródłowego. Obsługiwane regiony uniwersalnej repliki obejmują:

Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada środkowa, Kanada Wschodnia, środkowe stany USA, Azja Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowa, Północno-środkowe stany USA, Europa Północna, Południowo-środkowe stany USA, Zachodnie Zjednoczone Królestwo Południowe Zjednoczone Królestwo Azja Południowo-Wschodnia.

### <a name="paired-regions"></a>Sparowane regiony

Oprócz regionów uniwersalnej repliki można utworzyć replikę odczytu w sparowanym regionie platformy Azure na serwerze źródłowym. Jeśli nie znasz pary regionów, możesz dowiedzieć się więcej z [artykułu z sparowanymi regionami platformy Azure](../best-practices-availability-paired-regions.md).

Jeśli używasz replik między regionami do planowania odzyskiwania po awarii, zalecamy utworzenie repliki w sparowanym regionie, a nie w jednym z innych regionów. Sparowane regiony umożliwiają uniknięcie jednoczesnych aktualizacji i określanie priorytetów fizycznej izolacji i miejsca zamieszkania danych.  

Istnieją jednak ograniczenia, które należy wziąć pod uwagę: 

* Dostępność regionalna: Azure Database for MariaDB jest dostępna w regionach Francja środkowa, Zjednoczone Emiraty Arabskie i Niemcy środkowe. Jednak ich sparowane regiony nie są dostępne.

* Pary jednokierunkowe: niektóre regiony platformy Azure są sparowane tylko w jednym kierunku. Regiony te obejmują Indie Zachodnie, Brazylia Południowa i US Gov Wirginia. 
   Oznacza to, że serwer źródłowy w regionie zachodnie Indie może utworzyć replikę w Indiach Południowej. Jednak serwer źródłowy w Republice Południowej Indie nie może utworzyć repliki w Indiach zachodnim. Jest to spowodowane tym, że region pomocniczy w zachodniej Indiach to Indie Południowe, ale region pomocniczy w Republice Południowej Indie nie jest Indie Zachodnie.

## <a name="create-a-replica"></a>Tworzenie repliki

> [!IMPORTANT]
> Funkcja odczytu repliki jest dostępna tylko dla serwerów Azure Database for MariaDB w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer źródłowy znajduje się w jednej z tych warstw cenowych.

Jeśli serwer źródłowy nie ma istniejących serwerów repliki, źródło zostanie najpierw ponownie uruchomione w celu przygotowania się do replikacji.

Po uruchomieniu przepływu pracy tworzenia repliki zostanie utworzony pusty serwer Azure Database for MariaDB. Nowy serwer jest wypełniony danymi znajdującymi się na serwerze źródłowym. Czas utworzenia zależy od ilości danych w źródle oraz czasu od ostatniego cotygodniowej pełnej kopii zapasowej. Czas może się wahać od kilku minut do kilku godzin.

> [!NOTE]
> Jeśli nie masz skonfigurowanego alertu magazynu na serwerach, zalecamy wykonanie tej czynności. Alert informuje o tym, kiedy serwer zbliża się do limitu magazynu, co wpłynie na replikację.

Dowiedz się [, jak utworzyć replikę odczytu w Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Nawiązywanie połączenia z repliką

Podczas tworzenia repliki dziedziczy reguły zapory serwera źródłowego. Następnie te reguły są niezależne od serwera źródłowego.

Replika dziedziczy konto administratora z serwera źródłowego. Wszystkie konta użytkowników na serwerze źródłowym są replikowane do replik odczytu. Można nawiązać połączenie z repliką odczytu tylko przy użyciu kont użytkowników, które są dostępne na serwerze źródłowym.

Możesz połączyć się z repliką przy użyciu nazwy hosta i prawidłowego konta użytkownika, tak jak w przypadku zwykłego serwera Azure Database for MariaDB. W przypadku serwera o nazwie Moja **replika** z **nazwą administratora** administrator można nawiązać połączenie z repliką przy użyciu interfejsu wiersza polecenia MySQL:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

W wierszu polecenia wprowadź hasło dla konta użytkownika.

## <a name="monitor-replication"></a>Monitorowanie replikacji

Azure Database for MariaDB zapewnia wartość **opóźnienia replikacji w sekundach** w Azure monitor. Ta Metryka jest dostępna tylko dla replik.

Ta Metryka jest obliczana przy użyciu `seconds_behind_master` metryki dostępnej w MariaDB `SHOW SLAVE STATUS` polecenia.

Ustaw Alert, aby poinformować Cię, gdy zwłoka replikacji osiągnie wartość, która nie jest akceptowalna dla obciążenia.

## <a name="stop-replication"></a>Zatrzymywanie replikacji

Można zatrzymać replikację między źródłem a repliką. Po zatrzymaniu replikacji między serwerem źródłowym a repliką odczytu replika stanie się serwerem autonomicznym. Dane na serwerze autonomicznym to dane, które były dostępne w replice w momencie uruchomienia polecenia Zatrzymaj replikację. Serwer autonomiczny nie znajduje się na serwerze źródłowym.

Gdy zdecydujesz się zatrzymać replikację do repliki, utraci ona wszystkie linki do poprzednich źródeł i innych replik. Między źródłem i jego repliką nie ma automatycznej pracy awaryjnej.

> [!IMPORTANT]
> Serwer autonomiczny nie może zostać ponownie utworzony w replice.
> Przed zatrzymaniem replikacji w replice odczytu upewnij się, że replika ma wszystkie wymagane dane.

Dowiedz się, jak [zatrzymać replikację do repliki](howto-read-replicas-portal.md).

## <a name="failover"></a>Tryb failover

Nie istnieje automatyczna praca awaryjna między serwerami źródłowym i repliki.

Ponieważ replikacja jest asynchroniczna, między źródłem a repliką występuje opóźnienie. Na czas opóźnienia może wpływać wiele czynników, takich jak zmniejszanie obciążenia uruchomionego na serwerze źródłowym i opóźnienia między centrami danych. W większości przypadków opóźnienia repliki wynoszą od kilku sekund do kilku minut. Rzeczywiste opóźnienie replikacji można śledzić przy użyciu *opóźnienia repliki* metryk, które jest dostępne dla każdej repliki. Ta Metryka przedstawia czas od ostatniego odtworzonej transakcji. Zalecamy, aby określić, co to jest średnie opóźnienie, obserwując opóźnienie repliki w danym okresie czasu. Można ustawić alert w przypadku zwłoki repliki, aby w przypadku, gdy znajdzie się poza oczekiwanym zakresem, można wykonać akcję.

> [!Tip]
> W przypadku przejścia w tryb failover do repliki zwłoka w momencie odłączenia repliki od źródła będzie wskazywać, ile danych jest utraconych.

Po podjęciu decyzji o przejściu do trybu failover w replice

1. Zatrzymaj replikację do repliki.

   Ten krok jest niezbędny, aby serwer repliki mógł akceptować operacje zapisu. W ramach tego procesu serwer repliki zostanie odłączone od klasy podstawowej. Po zainicjowaniu zatrzymania replikacji proces zaplecza zwykle trwa około 2 minuty. Zapoznaj się z sekcją [Zatrzymaj replikację](#stop-replication) tego artykułu, aby poznać konsekwencje tej akcji.

2. Wskaż aplikację na (dawniej) replikę.

   Każdy serwer ma unikatowe parametry połączenia. Zaktualizuj swoją aplikację, tak aby wskazywała replikę (dawniej) zamiast podstawowej.

Po pomyślnym przetworzeniu odczytów i zapisów aplikacja została ukończona w trybie failover. Czas przestoju, w jakim zależą od aplikacji, będzie zależny od tego, kiedy wykryjesz problem, i wykonaj kroki 1 i 2 powyżej.

## <a name="considerations-and-limitations"></a>Istotne zagadnienia i ograniczenia

### <a name="pricing-tiers"></a>Warstwy cenowe

Repliki odczytu są obecnie dostępne tylko w warstwach cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

> [!NOTE]
> Koszt uruchomienia serwera repliki jest oparty na regionie, w którym jest uruchomiony serwer repliki.

### <a name="source-server-restart"></a>Ponowne uruchamianie serwera źródłowego

Podczas tworzenia repliki dla źródła, które nie ma istniejących replik, źródło zostanie najpierw ponownie uruchomione w celu przygotowania się do replikacji. Należy wziąć pod uwagę i wykonać te operacje w okresie poza szczytem.

### <a name="new-replicas"></a>Nowe repliki

Replika odczytu jest tworzona jako nowy serwer Azure Database for MariaDB. Nie można wykonać istniejącego serwera w replice. Nie można utworzyć repliki innej repliki odczytu.

### <a name="replica-configuration"></a>Konfiguracja repliki

Replika jest tworzona przy użyciu tej samej konfiguracji serwera co podstawowa. Po utworzeniu repliki kilka ustawień można zmienić niezależnie od serwera źródłowego: generowanie obliczeń, rdzeni wirtualnych, magazyn, okres przechowywania kopii zapasowej i wersja aparatu MariaDB. Warstwę cenową można także zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub z niej.

> [!IMPORTANT]
> Przed zaktualizowaniem konfiguracji serwera źródłowego do nowych wartości zaktualizuj konfigurację repliki do takich samych lub wyższych wartości. Ta akcja zapewnia, że replika może być zachowywana wraz ze wszystkimi zmianami wprowadzonymi do podstawowego.

Reguły zapory i ustawienia parametrów są dziedziczone z serwera źródłowego do repliki podczas tworzenia repliki. Następnie reguły repliki są niezależne.

### <a name="stopped-replicas"></a>Repliki zatrzymane

Jeśli zatrzymasz replikację między serwerem źródłowym i repliką odczytu, zatrzymana replika stanie się autonomicznym serwerem, który akceptuje oba operacje odczytu i zapisu. Serwer autonomiczny nie może zostać ponownie utworzony w replice.

### <a name="deleted-source-and-standalone-servers"></a>Usunięte serwery źródłowe i autonomiczne

Po usunięciu serwera źródłowego replikacja zostaje zatrzymana dla wszystkich replik odczytu. Te repliki automatycznie stają się serwerami autonomicznymi i mogą akceptować zarówno operacje odczytu, jak i zapisu. Serwer źródłowy jest usuwany.

### <a name="user-accounts"></a>Konta użytkowników

Użytkownicy na serwerze źródłowym są replikowana do replik odczytu. Można nawiązać połączenie z repliką odczytu tylko przy użyciu kont użytkowników dostępnych na serwerze źródłowym.

### <a name="server-parameters"></a>Parametry serwera

Aby zapobiec utracie synchronizacji danych i ich możliwej utracie lub uszkodzeniu, aktualizacja niektórych parametrów jest zablokowana w przypadku korzystania z replik do odczytu.

Następujące parametry serwera są blokowane zarówno na serwerze źródłowym, jak i w programie Replica:

* [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
* [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

[`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler)Parametr jest zablokowany na serwerach repliki.

Aby zaktualizować jeden z powyższych parametrów na serwerze źródłowym, Usuń serwery repliki, zaktualizuj wartość parametru w podstawowym i ponownie utwórz repliki.

### <a name="other"></a>Inne

* Tworzenie repliki repliki nie jest obsługiwane.
* Tabele w pamięci mogą spowodować, że repliki nie zostaną zsynchronizowane. Jest to ograniczenie technologii replikacji MariaDB.
* Upewnij się, że tabele serwera źródłowego mają klucze podstawowe. Brak kluczy podstawowych może spowodować opóźnienie replikacji między źródłem i replikami.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak tworzyć repliki odczytu i zarządzać nimi przy użyciu Azure Portal](howto-read-replicas-portal.md)
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure](howto-read-replicas-cli.md)
