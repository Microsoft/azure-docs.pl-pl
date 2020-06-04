---
title: Odczytaj repliki — Azure Database for MySQL.
description: 'Informacje na temat odczytu replik w Azure Database for MySQL: wybór regionów, tworzenie replik, łączenie z replikami, monitorowanie replikacji i zatrzymywanie replikacji.'
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 5/4/2020
ms.openlocfilehash: 1536773840693e943b1e32f0ae7358b8b2ee7ce9
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324644"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Repliki do odczytu w usłudze Azure Database for MySQL

Funkcja repliki do odczytu umożliwia replikowanie danych z serwera usługi Azure Database for MySQL do serwera tylko do odczytu. Z serwera głównego można replikować maksymalnie pięć replik. Repliki są aktualizowane asynchronicznie przy użyciu natywnej technologii replikacji aparatu programu MySQL opartej na pozycji w pliku dziennika binarnego (binlog). Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Repliki to nowe serwery, którymi można zarządzać podobnie jak regularne Azure Database for MySQL serwery. Dla każdej repliki odczytu są naliczane opłaty za zasoby obliczeniowe rdzeni wirtualnych i magazyn w GB/miesiąc.

Aby dowiedzieć się więcej na temat funkcji i problemów związanych z replikacją MySQL, zobacz [dokumentację dotyczącą replikacji MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>Kiedy używać repliki odczytu

Funkcja odczytu repliki pomaga zwiększyć wydajność i skalowalność obciążeń intensywnie korzystających z odczytu. Obciążenia odczytu mogą być odizolowane dla replik, podczas gdy obciążenia zapisu mogą być kierowane do wzorca.

Typowy scenariusz polega na tym, że obciążenia analizy biznesowej i analizy używają repliki odczytu jako źródła danych do raportowania.

Ponieważ repliki są tylko do odczytu, nie zmniejszają bezpośrednio obciążeń związanych z pojemnością zapisu na serwerze głównym. Ta funkcja nie jest przeznaczona dla obciążeń intensywnie korzystających z pisania.

Funkcja odczytu repliki korzysta z replikacji asynchronicznej MySQL. Ta funkcja nie jest przeznaczona do scenariuszy replikacji synchronicznej. Nastąpi wymierne opóźnienie między serwerem głównym a repliką. Dane z repliki ostatecznie staną się spójne z danymi na serwerze głównym. Użyj tej funkcji dla obciążeń, które mogą obsłużyć to opóźnienie.

## <a name="cross-region-replication"></a>Replikacja między regionami
Replikę odczytu można utworzyć w innym regionie niż serwer główny. Replikacja między regionami może być przydatna w scenariuszach takich jak planowanie odzyskiwania po awarii lub umieszczenie danych bliżej użytkowników.

Serwer główny może być w dowolnym [regionie Azure Database for MySQL](https://azure.microsoft.com/global-infrastructure/services/?products=mysql).  Serwer główny może mieć replikę w osobnym regionie lub regionach uniwersalnej repliki. Na poniższej ilustracji przedstawiono, które regiony replik są dostępne w zależności od regionu głównego.

[![Odczytaj regiony repliki](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiony uniwersalnej repliki
Replikę odczytu można utworzyć w dowolnym z następujących regionów, niezależnie od tego, gdzie znajduje się serwer główny. Obsługiwane regiony uniwersalnej repliki obejmują:

Australia Wschodnia, Australia Południowo-Wschodnia, środkowe stany USA, Azja Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowo-Wschodnia, Płn. Północno-środkowe stany USA, Europa Północna, Południowe stany USA, Azja Południowo-Wschodnia, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Europa Zachodnia i zachodnie stany USA.

* Zachodnie stany USA 2 są tymczasowo niedostępne jako lokalizacja repliki między regionami.

### <a name="paired-regions"></a>Sparowane regiony
Oprócz regionów uniwersalnej repliki można utworzyć replikę odczytu w sparowanym regionie platformy Azure serwera głównego. Jeśli nie znasz pary regionów, możesz dowiedzieć się więcej z [artykułu z sparowanymi regionami platformy Azure](../best-practices-availability-paired-regions.md).

Jeśli używasz replik między regionami do planowania odzyskiwania po awarii, zalecamy utworzenie repliki w sparowanym regionie, a nie w jednym z innych regionów. Sparowane regiony umożliwiają uniknięcie jednoczesnych aktualizacji i określanie priorytetów fizycznej izolacji i miejsca zamieszkania danych.  

Istnieją jednak ograniczenia, które należy wziąć pod uwagę: 

* Dostępność regionalna: Azure Database for MySQL jest dostępna w regionie zachodnie stany USA 2, Francja środkowa, Zjednoczone Emiraty Arabskie i Niemcy środkowe. Jednak ich sparowane regiony nie są dostępne.
    
* Pary jednokierunkowe: niektóre regiony platformy Azure są sparowane tylko w jednym kierunku. Regiony te obejmują Indie Zachodnie, Brazylia Południowa i US Gov Wirginia. 
   Oznacza to, że serwer główny w regionie zachodnie Indie może utworzyć replikę w Indiach Południowej. Jednak główny serwer nie może utworzyć repliki w Indiach zachodnim. Jest to spowodowane tym, że region pomocniczy w zachodniej Indiach to Indie Południowe, ale region pomocniczy w Republice Południowej Indie nie jest Indie Zachodnie.

## <a name="create-a-replica"></a>Tworzenie repliki

Jeśli serwer główny nie ma istniejących serwerów repliki, wzorzec zostanie najpierw uruchomiony ponownie w celu samodzielnego przygotowania do replikacji.

Po uruchomieniu przepływu pracy tworzenia repliki zostanie utworzony pusty serwer Azure Database for MySQL. Nowy serwer jest wypełniony danymi znajdującymi się na serwerze głównym. Czas utworzenia zależy od ilości danych na serwerze głównym oraz czasu od ostatniego cotygodniowej pełnej kopii zapasowej. Czas może się wahać od kilku minut do kilku godzin. Serwer repliki jest zawsze tworzony w tej samej grupie zasobów i tej samej subskrypcji co serwer główny. Jeśli chcesz utworzyć serwer repliki w innej grupie zasobów lub innej subskrypcji, możesz [przenieść serwer repliki](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) po utworzeniu.

Każda replika ma włączoną funkcję [autowzrostu](concepts-pricing-tiers.md#storage-auto-grow)magazynu. Funkcja autozwiększania umożliwia replikom przechowywanie danych replikowanych do nich i uniemożliwia przerwanie replikacji spowodowane błędami magazynu.

Dowiedz się [, jak utworzyć replikę odczytu w Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Nawiązywanie połączenia z repliką

Podczas tworzenia repliki dziedziczy reguły zapory serwera głównego. Następnie te reguły są niezależne od serwera głównego.

Replika dziedziczy konto administratora z serwera głównego. Wszystkie konta użytkowników na serwerze głównym są replikowane do replik odczytu. Można nawiązać połączenie z repliką odczytu tylko przy użyciu kont użytkowników, które są dostępne na serwerze głównym.

Możesz połączyć się z repliką przy użyciu nazwy hosta i prawidłowego konta użytkownika, tak jak w przypadku zwykłego serwera Azure Database for MySQL. W przypadku serwera o nazwie Moja **replika** z **nazwą administratora**administrator można nawiązać połączenie z repliką przy użyciu interfejsu wiersza polecenia MySQL:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

W wierszu polecenia wprowadź hasło dla konta użytkownika.

## <a name="monitor-replication"></a>Monitorowanie replikacji

Azure Database for MySQL zapewnia wartość **opóźnienia replikacji w sekundach** w Azure monitor. Ta Metryka jest dostępna tylko dla replik.

Ta Metryka jest obliczana przy użyciu `seconds_behind_master` metryki dostępnej w `SHOW SLAVE STATUS` poleceniu programu MySQL.

Ustaw Alert, aby poinformować Cię, gdy zwłoka replikacji osiągnie wartość, która nie jest akceptowalna dla obciążenia.

## <a name="stop-replication"></a>Zatrzymywanie replikacji

Można zatrzymać replikację między serwerem głównym a repliką. Po zatrzymaniu replikacji między serwerem głównym a repliką odczytu replika stanie się serwerem autonomicznym. Dane na serwerze autonomicznym to dane, które były dostępne w replice w momencie uruchomienia polecenia Zatrzymaj replikację. Serwer autonomiczny nie jest przechwytywany z serwerem głównym.

Gdy zdecydujesz się zatrzymać replikację do repliki, utraci ona wszystkie linki do poprzedniego wzorca i innych replik. Między wzorcem a jego repliką nie ma automatycznej pracy awaryjnej.

> [!IMPORTANT]
> Serwer autonomiczny nie może zostać ponownie utworzony w replice.
> Przed zatrzymaniem replikacji w replice odczytu upewnij się, że replika ma wszystkie wymagane dane.

Dowiedz się, jak [zatrzymać replikację do repliki](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Istotne zagadnienia i ograniczenia

### <a name="pricing-tiers"></a>Warstwy cenowe

Repliki odczytu są obecnie dostępne tylko w warstwach cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

> [!NOTE]
> Koszt uruchomienia serwera repliki jest oparty na regionie, w którym jest uruchomiony serwer repliki.

### <a name="master-server-restart"></a>Ponowne uruchamianie serwera głównego

Gdy tworzysz replikę dla wzorca, który nie ma istniejących replik, wzorzec zostanie najpierw uruchomiony ponownie w celu przygotowania się do replikacji. Należy wziąć pod uwagę i wykonać te operacje w okresie poza szczytem.

### <a name="new-replicas"></a>Nowe repliki

Replika odczytu jest tworzona jako nowy serwer Azure Database for MySQL. Nie można wykonać istniejącego serwera w replice. Nie można utworzyć repliki innej repliki odczytu.

### <a name="replica-configuration"></a>Konfiguracja repliki

Replika jest tworzona przy użyciu tej samej konfiguracji serwera co serwer główny. Po utworzeniu repliki można zmienić kilka ustawień niezależnie od serwera głównego: generowanie obliczeń, rdzeni wirtualnych, magazyn i okres przechowywania kopii zapasowych. Warstwę cenową można także zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub z niej.

> [!IMPORTANT]
> Przed zaktualizowaniem konfiguracji serwera głównego do nowych wartości zaktualizuj konfigurację repliki do takich samych lub wyższych wartości. Dzięki temu replika może być na bieżąco ze zmianami wprowadzonymi we wzorcu.

Reguły zapory i ustawienia parametrów są dziedziczone z serwera głównego do repliki podczas tworzenia repliki. Następnie reguły repliki są niezależne.

### <a name="stopped-replicas"></a>Repliki zatrzymane

Jeśli zatrzymasz replikację między serwerem głównym a repliką odczytu, zatrzymana replika stanie się autonomicznym serwerem, który akceptuje oba operacje odczytu i zapisu. Serwer autonomiczny nie może zostać ponownie utworzony w replice.

### <a name="deleted-master-and-standalone-servers"></a>Usunięto serwery główne i autonomiczne

Po usunięciu serwera głównego replikacja zostaje zatrzymana dla wszystkich replik odczytu. Te repliki automatycznie stają się serwerami autonomicznymi i mogą akceptować zarówno operacje odczytu, jak i zapisu. Sam serwer główny jest usuwany.

### <a name="user-accounts"></a>Konta użytkowników

Użytkownicy na serwerze głównym są replikowana do replik odczytu. Można nawiązać połączenie z repliką odczytu tylko przy użyciu kont użytkowników dostępnych na serwerze głównym.

### <a name="server-parameters"></a>Parametry serwera

Aby zapobiec utracie synchronizacji danych i ich możliwej utracie lub uszkodzeniu, aktualizacja niektórych parametrów jest zablokowana w przypadku korzystania z replik do odczytu.

Następujące parametry serwera są blokowane na serwerach głównych i repliki:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

[`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler)Parametr jest zablokowany na serwerach repliki. 

Aby zaktualizować jeden z powyższych parametrów na serwerze głównym, należy usunąć serwer repliki, zaktualizować wartość parametru na wzorcu i ponownie utworzyć repliki.

### <a name="other"></a>Inne

- Globalne identyfikatory transakcji (GTID) nie są obsługiwane.
- Tworzenie repliki repliki nie jest obsługiwane.
- Tabele w pamięci mogą spowodować, że repliki nie zostaną zsynchronizowane. Jest to ograniczenie technologii replikacji MySQL. Więcej informacji można znaleźć w [dokumentacji programu MySQL Reference](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) .
- Upewnij się, że tabele serwera głównego mają klucze podstawowe. Brak kluczy podstawowych może spowodować opóźnienia replikacji między serwerem głównym a replikami.
- Zapoznaj się z pełną listą ograniczeń replikacji MySQL w [dokumentacji programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak tworzyć repliki odczytu i zarządzać nimi przy użyciu Azure Portal](howto-read-replicas-portal.md)
- Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure](howto-read-replicas-cli.md)
