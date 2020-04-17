---
title: Odczyt replik — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano funkcję repliki odczytu w usłudze Azure Database for PostgreSQL — Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 545d04bdede76a6ce25c9e4665f39c01ff6caa73
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531987"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Odczyt replik w usłudze Azure Database for PostgreSQL — pojedynczy serwer

Funkcja repliki odczytu umożliwia replikowanie danych z usługi Azure Database dla serwera PostgreSQL do serwera tylko do odczytu. Z serwera głównego można replikować maksymalnie pięć replik. Repliki są aktualizowane asynchronicznie za pomocą technologii replikacji natywnej aparatu PostgreSQL.

Repliki to nowe serwery, którymi zarządzasz, podobnie jak zwykłe usługi Azure Database dla serwerów PostgreSQL. Dla każdej repliki odczytu są naliczane płatności za aprowizowanych obliczeń w rach wirtualnych i magazynu w GB/ miesiąc.

Dowiedz się, jak [tworzyć repliki i zarządzać nimi.](howto-read-replicas-portal.md)

## <a name="when-to-use-a-read-replica"></a>Kiedy używać repliki odczytu
Funkcja repliki odczytu pomaga poprawić wydajność i skalę obciążeń intensywnie korzystających z odczytu. Obciążeń odczytu mogą być izolowane do replik, podczas gdy obciążenia zapisu mogą być kierowane do wzorca.

Typowym scenariuszem jest, aby obciążenia analizy biznesowej i analityczne używały repliki odczytu jako źródła danych do raportowania.

Ponieważ repliki są tylko do odczytu, nie bezpośrednio zmniejszyć obciążenia pojemności zapisu na wzorzec. Ta funkcja nie jest przeznaczona dla obciążeń intensywnie korzystających z zapisu.

Funkcja repliki odczytu używa replikacji asynchroniiowej PostgreSQL. Ta funkcja nie jest przeznaczona dla scenariuszy replikacji synchroniczowej. Pomiędzy wzorcem a repliką wystąpi wymierne opóźnienie. Dane w replice ostatecznie staje się zgodne z danymi na wzorcu. Ta funkcja służy do obciążeń, które mogą pomieścić to opóźnienie.

## <a name="cross-region-replication"></a>Replikacja między regionami
Replikę odczytu można utworzyć w innym regionie niż serwer główny. Replikacja między regionami może być przydatna w scenariuszach, takich jak planowanie odzyskiwania po awarii lub przybliżenie danych użytkownikom.

Serwer główny można mieć w dowolnej [bazie danych platformy Azure dla regionu PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Serwer główny może mieć replikę w jego sparowanym regionie lub regionach repliki uniwersalnej. Poniższy rysunek przedstawia, które regiony repliki są dostępne w zależności od regionu głównego.

[![Odczyt regionów repliki](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiony replik uniwersalnych
Zawsze można utworzyć replikę odczytu w dowolnym z następujących regionów, niezależnie od tego, gdzie znajduje się serwer główny. Są to uniwersalne regiony repliki:

Australia Wschodnia, Australia Południowo-Wschodnia, Środkowe Stany Zjednoczone, Azja Wschodnia, Wschodnie STANY USA, Wschodnie STANY USA 2, Japonia Wschodnia, Japonia Zachodnia, Korea Środkowa, Korea Południowa, Północno-Środkowe STANY USA, Europa Północna, Południowo-Środkowe Stany Zjednoczone, Azja Południowo-Wschodnia, Wielka Brytania Południowa, Wielka Brytania Zachodnia, Europa Zachodnia, Zachodnie stany USA.

*Zachodnie stany USA 2 są tymczasowo niedostępne jako lokalizacja repliki międzyregionami.


### <a name="paired-regions"></a>Sparowane regiony
Oprócz regionów repliki uniwersalnej można utworzyć replikę odczytu w sparowanym regionie platformy Azure serwera głównego. Jeśli nie znasz pary regionu, możesz dowiedzieć się więcej z [artykułu Sparowane regiony platformy Azure](../best-practices-availability-paired-regions.md).

Jeśli używasz replik między regionami do planowania odzyskiwania po awarii, zaleca się utworzenie repliki w sparowanym regionie zamiast w jednym z innych regionów. Sparowane regiony unikają jednoczesnych aktualizacji i ustalają priorytety izolacji fizycznej i rezydencji danych.  

Istnieją ograniczenia, które należy wziąć pod uwagę: 

* Dostępność regionalna: usługa Azure Database for PostgreSQL jest dostępna w zachodnie stany USA 2, Francja Central, Zjednoczone Emiraty Północne i Niemcy Środkowe. Jednak ich sparowane regiony nie są dostępne.
    
* Pary jednokierunkowe: niektóre regiony platformy Azure są sparowane tylko w jednym kierunku. Regiony te obejmują Indie Zachodnie, Brazylia Południowa. 
   Oznacza to, że serwer główny w Indiach Zachodnich może utworzyć replikę w południowych Indiach. Jednak serwer główny w południowych Indiach nie może utworzyć repliki w Indiach Zachodnich. Dzieje się tak dlatego, że drugim regionem Indii Zachodnich są Południowe Indie, ale drugorzędnym regionem Południowych Indii nie są Indie Zachodnie.


## <a name="create-a-replica"></a>Tworzenie repliki
Po uruchomieniu przepływu pracy repliki tworzony jest pusty serwer usługi Azure Database dla postgreSql. Nowy serwer jest wypełniony danymi, które znajdowały się na serwerze głównym. Czas tworzenia zależy od ilości danych w wzorcu i czasu od ostatniej cotygodniowej pełnej kopii zapasowej. Czas może wynosić od kilku minut do kilku godzin.

Każda replika jest włączona do [automatycznego powiększania](concepts-pricing-tiers.md#storage-auto-grow)pamięci masowej. Funkcja automatycznego powiększania umożliwia repliki nadążanie za danymi replikowanymi do niej i zapobieganie przerwaniu replikacji spowodowanemu błędami braku pamięci masowej.

Funkcja repliki odczytu używa replikacji fizycznej PostgreSQL, a nie replikacji logicznej. Replikacja przesyłania strumieniowego przy użyciu gniazd replikacji jest domyślnym trybem pracy. W razie potrzeby wysyłka dziennika jest używana do nadrobienia zaległości.

Dowiedz się, jak [utworzyć replikę odczytu w witrynie Azure portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Łączenie się z repliką
Podczas tworzenia repliki nie dziedziczy reguł zapory lub punktu końcowego usługi sieci wirtualnej serwera głównego. Reguły te muszą być skonfigurowane niezależnie dla repliki.

Replika dziedziczy konto administratora z serwera głównego. Wszystkie konta użytkowników na serwerze głównym są replikowane do replik odczytu. Z repliką odczytu można łączyć się tylko przy użyciu kont użytkowników dostępnych na serwerze głównym.

Można połączyć się z repliką przy użyciu jej nazwy hosta i prawidłowego konta użytkownika, tak jak w przypadku zwykłej bazy danych Azure database dla serwera PostgreSQL. Dla serwera o nazwie **moja replika** z adminem nazwa użytkownika **myadmin**, można połączyć się z repliką za pomocą psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

W wierszu polecenia wprowadź hasło do konta użytkownika.

## <a name="monitor-replication"></a>Monitorowanie replikacji
Usługa Azure Database for PostgreSQL udostępnia dwie metryki do monitorowania replikacji. Dwa metryki to **Max Lag Across Replicas** i **Replica Lag**. Aby dowiedzieć się, jak wyświetlić te metryki, zobacz **sekcję Monitorowanie repliki** [w artykule instrukcje odczytu repliki](howto-read-replicas-portal.md).

**Maksymalna liczba opóźnień w replikach** pokazuje opóźnienie w bajtach między wzorcem a repliką najbardziej opóźnionych. Ta metryka jest dostępna tylko na serwerze głównym.

**Metryka Opóźnienie repliki** pokazuje czas od ostatniej powtórzonejedno transakcji. Jeśli na serwerze głównym nie ma żadnych transakcji, metryka odzwierciedla opóźnienie tego czasu. Ta metryka jest dostępna tylko dla serwerów replik. Opóźnienie repliki jest `pg_stat_wal_receiver` obliczane na podstawie widoku:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Ustaw alert informujący, gdy opóźnienie repliki osiągnie wartość, która nie jest do przyjęcia dla obciążenia. 

Aby uzyskać dodatkowe informacje, należy bezpośrednio zbadać serwer główny, aby uzyskać opóźnienie replikacji w bajtach na wszystkich replikach.

W PostgreSQL w wersji 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

W PostgreSQL w wersji 9.6 i wcześniejszych:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Jeśli serwer główny lub replika odczytu zostanie ponownie uruchomiona, czas potrzebny do ponownego uruchomienia i nadrobienia zaległości zostanie odzwierciedlony w metryce Opóźnienie repliki.

## <a name="stop-replication"></a>Zatrzymywanie replikacji
Można zatrzymać replikację między wzorcem a repliką. Akcja zatrzymania powoduje ponowne uruchomienie repliki i usunięcie jej ustawień replikacji. Po zatrzymaniu replikacji między serwerem głównym a repliką odczytu replika staje się serwerem autonomicznym. Dane na serwerze autonomicznym są danymi, które były dostępne w replice w momencie rozpoczęcia polecenia stop replikacji. Serwer autonomiczny nie nadrabia zaległości z serwerem głównym.

> [!IMPORTANT]
> Nie można ponownie przekształcić serwera autonomicznego w replikę.
> Przed zatrzymaniem repliki w replice odczytu upewnij się, że replika ma wszystkie wymagane dane.

Po zatrzymaniu replikacji replika traci wszystkie łącza do poprzedniego wzorca i innych replik.

Dowiedz się, jak [zatrzymać replikę repliki](howto-read-replicas-portal.md).

## <a name="failover"></a>Tryb failover
Nie ma automatycznego trybu failover między serwerami głównymi i replikami. 

Ponieważ replikacja jest asynchroniza, występuje opóźnienie między wzorcem a repliką. Na ilość opóźnień może mieć wpływ wiele czynników, takich jak obciążenie uruchomione na serwerze głównym i opóźnienie między centrami danych. W większości przypadków opóźnienie repliki waha się od kilku sekund do kilku minut. Rzeczywiste opóźnienie replikacji można śledzić za pomocą metryki *Opóźnienie repliki,* która jest dostępna dla każdej repliki. Ta metryka pokazuje czas od ostatniej powtórzonejedno transakcji. Zaleca się zidentyfikowanie, jakie jest średnie opóźnienie, obserwując opóźnienie repliki w określonym czasie. Alert można ustawić na opóźnienie repliki, tak aby jeśli wykracza poza oczekiwany zakres, można podjąć działania.

> [!Tip]
> Jeśli powiększysz po awarii do repliki, opóźnienie w momencie odłączenia repliki od wzorca wskaże, ile danych zostanie utraconych.

Po podjęciu decyzji, że chcesz przejść w stan failover do repliki, 

1. Zatrzymywać replikację do repliki<br/>
   Ten krok jest konieczne, aby serwer repliki mógł akceptować zapisy. W ramach tego procesu serwer repliki zostanie ponownie uruchomiony i zostanie odłączony od wzorca. Po zainicjowaniu replikacji zatrzymania proces wewnętrznej bazy danych zwykle trwa około 2 minut. Zobacz sekcję [zatrzymaj replikację](#stop-replication) tego artykułu, aby zrozumieć implikacje tej akcji.
    
2. Wskaż aplikację do repliki (dawnej)<br/>
   Każdy serwer ma unikatowy ciąg połączenia. Zaktualizuj aplikację, aby wskazywała replikę (dawną) zamiast wzorca.
    
Po pomyślnym przetworzeniu aplikacji odczytuje i zapisuje, po zakończeniu pracy awaryjnej. Ilość przestojów aplikacji będzie zależeć od wykrycia problemu i wykonania kroków 1 i 2 powyżej.


## <a name="considerations"></a>Zagadnienia do rozważenia

W tej sekcji podsumowano zagadnienia dotyczące funkcji repliki odczytu.

### <a name="prerequisites"></a>Wymagania wstępne
Przed utworzeniem repliki `azure.replication_support` odczytu parametr musi być ustawiony na **replikę** na serwerze głównym. Po zmianie tego parametru ponowne uruchomienie serwera jest wymagane, aby zmiana została wniesiena. Parametr `azure.replication_support` dotyczy tylko warstw ogólnego przeznaczenia i zoptymalizowanej pamięci.

### <a name="new-replicas"></a>Nowe repliki
Replika odczytu jest tworzona jako nowa usługa Azure Database dla serwera PostgreSQL. Istniejącego serwera nie można przekształcić w replikę. Nie można utworzyć repliki innej repliki odczytu.

### <a name="replica-configuration"></a>Konfiguracja repliki
Replika jest tworzona przy użyciu tych samych ustawień obliczeniowych i magazynu, co wzorzec. Po utworzeniu repliki można zmienić kilka ustawień niezależnie od serwera głównego: generowanie obliczeń, wirtualne, magazynowanie i okres przechowywania kopii zapasowych. Warstwę cenową można również zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub podstawowa.

> [!IMPORTANT]
> Przed zaktualizowaniem ustawienia głównego do nowej wartości należy zaktualizować konfigurację repliki do wartości równej lub większej. Dzięki temu replika może być na bieżąco ze zmianami wprowadzonymi we wzorcu.

PostgreSQL wymaga, aby `max_connections` wartość parametru w replice odczytu była większa lub równa wartości wzorcowej; w przeciwnym razie replika nie zostanie uruchomiony. W usłudze Azure Database for `max_connections` PostgreSQL wartość parametru jest oparta na jednostce SKU. Aby uzyskać więcej informacji, zobacz [Limity w usłudze Azure Database for PostgreSQL](concepts-limits.md). 

Jeśli spróbujesz zaktualizować wartości serwera opisane powyżej, ale nie przestrzegasz limitów, zostanie wyświetlony błąd.

Reguły zapory, reguły sieci wirtualnej i ustawienia parametrów nie są dziedziczone z serwera głównego do repliki podczas tworzenia repliki lub po jej zakończeniu.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL wymaga,](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) aby `max_prepared_transactions` wartość parametru w replice odczytu była większa lub równa wartości wzorcowej; w przeciwnym razie replika nie zostanie uruchomiony. Jeśli chcesz zmienić `max_prepared_transactions` na wzorcu, najpierw zmień go w replikach.

### <a name="stopped-replicas"></a>Zatrzymane repliki
Jeśli zostanie zatrzymana replika między serwerem głównym a repliką odczytu, replika zostanie ponownie uruchomiona, aby zastosować zmianę. Zatrzymana replika staje się autonomicznym serwerem, który akceptuje zarówno odczyty, jak i zapisy. Nie można ponownie przekształcić serwera autonomicznego w replikę.

### <a name="deleted-master-and-standalone-servers"></a>Usunięte serwery główne i autonomiczne
Po usunięciu serwera głównego wszystkie jego repliki odczytu stają się serwerami autonomicznymi. Repliki są ponownie uruchamiane, aby odzwierciedlić tę zmianę.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi w portalu Azure.](howto-read-replicas-portal.md)
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi w interfejsie API interfejsu wiersza polecenia platformy Azure i rest.](howto-read-replicas-cli.md)
