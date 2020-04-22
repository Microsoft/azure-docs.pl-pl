---
title: Odczyt replik — usługa Azure Database for MySQL.
description: 'Dowiedz się więcej o odczytywaniu replik w usłudze Azure Database for MySQL: wybieraniu regionów, tworzeniu replik, łączeniu się z replikami, monitorowaniu replikacji i zatrzymywaniu replikacji.'
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 47f686f810f62fe03a9b0217677c436f3b91782b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767891"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Repliki do odczytu w usłudze Azure Database for MySQL

Funkcja repliki do odczytu umożliwia replikowanie danych z serwera usługi Azure Database for MySQL do serwera tylko do odczytu. Z serwera głównego można replikować maksymalnie pięć replik. Repliki są aktualizowane asynchronicznie przy użyciu natywnej technologii replikacji aparatu programu MySQL opartej na pozycji w pliku dziennika binarnego (binlog). Aby dowiedzieć się więcej o replikacji binlog, zobacz [omówienie replikacji binlogu MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Repliki to nowe serwery, którymi zarządzasz, podobnie jak zwykłe serwery usługi Azure Database dla serwerów MySQL. Dla każdej repliki odczytu są naliczane płatności za aprowizowanych obliczeń w rach wirtualnych i magazynu w GB/ miesiąc.

Aby dowiedzieć się więcej o funkcjach i problemach z replikacją MySQL, zobacz [dokumentację replikacji MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>Kiedy używać repliki odczytu

Funkcja repliki odczytu pomaga poprawić wydajność i skalę obciążeń intensywnie korzystających z odczytu. Obciążeń odczytu mogą być izolowane do replik, podczas gdy obciążenia zapisu mogą być kierowane do wzorca.

Typowym scenariuszem jest, aby obciążenia analizy biznesowej i analityczne używały repliki odczytu jako źródła danych do raportowania.

Ponieważ repliki są tylko do odczytu, nie bezpośrednio zmniejszyć obciążenia pojemności zapisu na wzorzec. Ta funkcja nie jest przeznaczona dla obciążeń intensywnie korzystających z zapisu.

Funkcja repliki odczytu używa replikacji asynchroniiowej MySQL. Ta funkcja nie jest przeznaczona dla scenariuszy replikacji synchroniczowej. Pomiędzy wzorcem a repliką wystąpi wymierne opóźnienie. Dane w replice ostatecznie staje się zgodne z danymi na wzorcu. Ta funkcja służy do obciążeń, które mogą pomieścić to opóźnienie.

## <a name="cross-region-replication"></a>Replikacja między regionami
Replikę odczytu można utworzyć w innym regionie niż serwer główny. Replikacja między regionami może być przydatna w scenariuszach, takich jak planowanie odzyskiwania po awarii lub przybliżenie danych użytkownikom.

Serwer główny można mieć w dowolnej [bazie danych platformy Azure dla regionu MySQL](https://azure.microsoft.com/global-infrastructure/services/?products=mysql).  Serwer główny może mieć replikę w jego sparowanym regionie lub regionach repliki uniwersalnej. Poniższy rysunek przedstawia, które regiony repliki są dostępne w zależności od regionu głównego.

[![Odczyt regionów repliki](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiony replik uniwersalnych
Replikę odczytu można utworzyć w dowolnym z następujących regionów, niezależnie od tego, gdzie znajduje się serwer główny. Obsługiwane regiony repliki uniwersalnej obejmują:

Australia Wschodnia, Australia Południowo-Wschodnia, Środkowe Stany Zjednoczone, Azja Wschodnia, Wschodnie STANY USA, Wschodnie STANY USA 2, Japonia Wschodnia, Japonia Zachodnia, Korea Środkowa, Korea Południowa, Północno-Środkowe STANY USA, Europa Północna, Południowo-Środkowe Stany Zjednoczone, Azja Południowo-Wschodnia, Wielka Brytania Południowa, Wielka Brytania Zachodnia, Europa Zachodnia, Zachodnie stany USA.

*Zachodnie stany USA 2 są tymczasowo niedostępne jako lokalizacja repliki międzyregionami.

### <a name="paired-regions"></a>Sparowane regiony
Oprócz regionów repliki uniwersalnej można utworzyć replikę odczytu w sparowanym regionie platformy Azure serwera głównego. Jeśli nie znasz pary regionu, możesz dowiedzieć się więcej z [artykułu Sparowane regiony platformy Azure](../best-practices-availability-paired-regions.md).

Jeśli używasz replik między regionami do planowania odzyskiwania po awarii, zaleca się utworzenie repliki w sparowanym regionie zamiast w jednym z innych regionów. Sparowane regiony unikają jednoczesnych aktualizacji i ustalają priorytety izolacji fizycznej i rezydencji danych.  

Istnieją jednak ograniczenia, które należy wziąć pod uwagę: 

* Dostępność regionalna: usługa Azure Database for MySQL jest dostępna w zachodnie stany USA 2, Francja Central, Zjednoczone Emiraty Północne i Niemcy Środkowe. Jednak ich sparowane regiony nie są dostępne.
    
* Pary jednokierunkowe: niektóre regiony platformy Azure są sparowane tylko w jednym kierunku. Regiony te obejmują Indie Zachodnie, Brazylia Południowa i US Gov Virginia. 
   Oznacza to, że serwer główny w Indiach Zachodnich może utworzyć replikę w południowych Indiach. Jednak serwer główny w południowych Indiach nie może utworzyć repliki w Indiach Zachodnich. Dzieje się tak dlatego, że drugim regionem Indii Zachodnich są Południowe Indie, ale drugorzędnym regionem Południowych Indii nie są Indie Zachodnie.


## <a name="create-a-replica"></a>Tworzenie repliki

Jeśli serwer główny nie ma istniejących serwerów replik, wzorzec najpierw uruchomi się ponownie, aby przygotować się do replikacji.

Po uruchomieniu przepływu pracy repliki tworzone jest pusta usługa Azure Database dla serwera MySQL. Nowy serwer jest wypełniony danymi, które znajdowały się na serwerze głównym. Czas tworzenia zależy od ilości danych w wzorcu i czasu od ostatniej cotygodniowej pełnej kopii zapasowej. Czas może wynosić od kilku minut do kilku godzin.

Każda replika jest włączona do [automatycznego powiększania](concepts-pricing-tiers.md#storage-auto-grow)pamięci masowej. Funkcja automatycznego powiększania umożliwia repliki nadążanie za danymi replikowanymi do niej i zapobieganie przerwaniom replikacji spowodowanym błędami braku miejsca do magazynowania.

Dowiedz się, jak [utworzyć replikę odczytu w witrynie Azure portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Łączenie się z repliką

Podczas tworzenia replika dziedziczy reguły zapory serwera głównego. Następnie te reguły są niezależne od serwera głównego.

Replika dziedziczy konto administratora z serwera głównego. Wszystkie konta użytkowników na serwerze głównym są replikowane do replik odczytu. Z repliką odczytu można łączyć się tylko przy użyciu kont użytkowników dostępnych na serwerze głównym.

Można połączyć się z repliką przy użyciu jej nazwy hosta i prawidłowego konta użytkownika, tak jak w przypadku zwykłej bazy danych Platformy Azure dla serwera MySQL. Dla serwera o nazwie **myreplica** z adminem nazwa użytkownika **myadmin**, można połączyć się z repliką za pomocą mysql CLI:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

W wierszu polecenia wprowadź hasło do konta użytkownika.

## <a name="monitor-replication"></a>Monitorowanie replikacji

Usługa Azure Database for MySQL udostępnia **metrykę replikacji w sekundach** w usłudze Azure Monitor. Ta metryka jest dostępna tylko dla replik.

Ta metryka jest `seconds_behind_master` obliczana przy użyciu metryki dostępnej w `SHOW SLAVE STATUS` poleceniu MySQL.

Ustaw alert informujący, gdy opóźnienie replikacji osiągnie wartość, która nie jest do przyjęcia dla obciążenia.

## <a name="stop-replication"></a>Zatrzymywanie replikacji

Można zatrzymać replikację między wzorcem a repliką. Po zatrzymaniu replikacji między serwerem głównym a repliką odczytu replika staje się serwerem autonomicznym. Dane na serwerze autonomicznym są danymi, które były dostępne w replice w momencie rozpoczęcia polecenia stop replikacji. Serwer autonomiczny nie nadrabia zaległości z serwerem głównym.

Po wybraniu zatrzymania replikacji do repliki, traci wszystkie łącza do poprzedniego wzorca i innych replik. Nie ma automatycznego trybu failover między wzorcem a jego repliką.

> [!IMPORTANT]
> Nie można ponownie przekształcić serwera autonomicznego w replikę.
> Przed zatrzymaniem repliki w replice odczytu upewnij się, że replika ma wszystkie wymagane dane.

Dowiedz się, jak [zatrzymać replikę repliki](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Istotne kwestie i ograniczenia

### <a name="pricing-tiers"></a>Warstwy cenowe

Repliki odczytu są obecnie dostępne tylko w warstwach cenowych ogólnego przeznaczenia i zoptymalizowanej pod kątem pamięci.

### <a name="master-server-restart"></a>Ponowne uruchomienie serwera głównego

Podczas tworzenia repliki dla wzorca, który nie ma istniejących replik, wzorzec najpierw uruchomi się ponownie, aby przygotować się do replikacji. Należy wziąć to pod uwagę i wykonać te operacje w okresie poza szczytem.

### <a name="new-replicas"></a>Nowe repliki

Replika odczytu jest tworzona jako nowa usługa Azure Database dla serwera MySQL. Istniejącego serwera nie można przekształcić w replikę. Nie można utworzyć repliki innej repliki odczytu.

### <a name="replica-configuration"></a>Konfiguracja repliki

Replika jest tworzona przy użyciu tej samej konfiguracji serwera co wzorzec. Po utworzeniu repliki można zmienić kilka ustawień niezależnie od serwera głównego: generowanie obliczeń, wirtualne, magazynowanie i okres przechowywania kopii zapasowych. Warstwę cenową można również zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub podstawowa.

> [!IMPORTANT]
> Przed zaktualizowaniem konfiguracji serwera głównego do nowych wartości zaktualizuj konfigurację repliki do takich samych lub wyższych wartości. Dzięki temu replika może być na bieżąco ze zmianami wprowadzonymi we wzorcu.

Reguły zapory i ustawienia parametrów są dziedziczone z serwera głównego do repliki podczas tworzenia repliki. Następnie zasady repliki są niezależne.

### <a name="stopped-replicas"></a>Zatrzymane repliki

Jeśli zostanie zatrzymana replika między serwerem głównym a repliką odczytu, zatrzymana replika stanie się serwerem autonomicznym, który akceptuje zarówno odczyty, jak i zapisy. Nie można ponownie przekształcić serwera autonomicznego w replikę.

### <a name="deleted-master-and-standalone-servers"></a>Usunięte serwery główne i autonomiczne

Po usunięciu serwera głównego replikacja jest zatrzymywana dla wszystkich replik odczytu. Repliki te automatycznie stają się autonomicznymi serwerami i mogą akceptować zarówno odczyty, jak i zapisy. Sam serwer główny zostanie usunięty.

### <a name="user-accounts"></a>Konta użytkowników

Użytkownicy na serwerze głównym są replikowane do replik odczytu. Z repliką odczytu można łączyć się tylko przy użyciu kont użytkowników dostępnych na serwerze głównym.

### <a name="server-parameters"></a>Parametry serwera

Aby zapobiec utracie synchronizacji danych i ich możliwej utracie lub uszkodzeniu, aktualizacja niektórych parametrów jest zablokowana w przypadku korzystania z replik do odczytu.

Następujące parametry serwera są zablokowane zarówno na serwerach wzorcowych, jak i repliki:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

Parametr [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) jest zablokowany na serwerach replik. 

### <a name="other"></a>Inne

- Globalne identyfikatory transakcji (GTID) nie są obsługiwane.
- Tworzenie repliki repliki nie jest obsługiwane.
- Tabele w pamięci może spowodować repliki stają się zsynchronizowane. Jest to ograniczenie technologii replikacji MySQL. Więcej informacji można znaleźć w [dokumentacji referencyjnej MySQL.](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html)
- Upewnij się, że tabele serwera głównego mają klucze podstawowe. Brak kluczy podstawowych może spowodować opóźnienie replikacji między wzorcem a replikami.
- Przejrzyj pełną listę ograniczeń replikacji MySQL w [dokumentacji MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi za pomocą portalu Azure](howto-read-replicas-portal.md)
- Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi przy użyciu interfejsu API interfejsu wiersza polecenia platformy Azure i interfejsu REST](howto-read-replicas-cli.md)
