---
title: Najlepsze praktyki dotyczące projektowania aplikacji — Azure Database for PostgreSQL
description: Poznaj najlepsze rozwiązania dotyczące tworzenia aplikacji przy użyciu Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: 6463f30bc79d937bd5a51a5c8c78fbdd72954b1e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364604"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-postgresql"></a>Najlepsze rozwiązania dotyczące kompilowania aplikacji za pomocą Azure Database for PostgreSQL

Poniżej przedstawiono niektóre najlepsze rozwiązania ułatwiające tworzenie aplikacji gotowych do użycia w chmurze za pomocą Azure Database for PostgreSQL. Te najlepsze rozwiązania mogą skrócić czas projektowania aplikacji.

## <a name="configuration-of-application-and-database-resources"></a>Konfiguracja zasobów aplikacji i bazy danych

### <a name="keep-the-application-and-database-in-the-same-region"></a>Utrzymywanie aplikacji i bazy danych w tym samym regionie
Upewnij się, że wszystkie zależności są w tym samym regionie podczas wdrażania aplikacji na platformie Azure. Rozmieszczenie wystąpień między regionami lub strefami dostępności powoduje utworzenie opóźnienia sieci, co może mieć wpływ na ogólną wydajność aplikacji.

### <a name="keep-your-postgresql-server-secure"></a>Dbaj o bezpieczeństwo serwera PostgreSQL
Skonfiguruj serwer PostgreSQL jako [bezpieczny](./concepts-security.md) i niedostępny publicznie. Aby zabezpieczyć serwer, użyj jednej z następujących opcji:
- [Reguły zapory](./concepts-firewall-rules.md)
- [Sieci wirtualne](./concepts-data-access-and-security-vnet.md)
- [Link prywatny platformy Azure](./concepts-data-access-and-security-private-link.md)

W celu zapewnienia bezpieczeństwa należy zawsze nawiązać połączenie z serwerem PostgreSQL za pośrednictwem protokołu SSL i skonfigurować serwer PostgreSQL i aplikację do korzystania z protokołu TLS 1,2. Zobacz [jak skonfigurować protokół SSL/TLS](./concepts-ssl-connection-security.md).

### <a name="tune-your-server-parameters"></a>Dostrajanie parametrów serwera
W przypadku obciążeń intensywnie korzystających z odczytu dostraja parametry serwera `tmp_table_size` i `max_heap_table_size` może pomóc zoptymalizować wydajność. Aby obliczyć wartości wymagane dla tych zmiennych, należy przyjrzeć się łącznym wartościom pamięci dla poszczególnych połączeń i pamięci podstawowej. Suma parametrów pamięci dla poszczególnych połączeń, z wyłączeniem `tmp_table_size` , w połączeniu z kontami pamięci podstawowej dla całkowitej ilości pamięci serwera.

### <a name="use-environment-variables-for-connection-information"></a>Użyj zmiennych środowiskowych w celu uzyskania informacji o połączeniu
Nie zapisuj poświadczeń bazy danych w kodzie aplikacji. W zależności od aplikacji frontonu postępuj zgodnie ze wskazówkami, aby skonfigurować zmienne środowiskowe. Aby skorzystać z usługi App Service, zobacz[jak skonfigurować ustawienia aplikacji](../app-service/configure-common.md#configure-app-settings) i dla usługi Azure Kuberentes, zobacz [jak używać wpisów tajnych Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/).

## <a name="performance-and-resiliency"></a>Wydajność i odporność
Poniżej przedstawiono kilka narzędzi i praktyk, których można użyć w celu ułatwienia debugowania problemów z wydajnością aplikacji.

### <a name="use-connection-pooling"></a>Korzystanie z puli połączeń
W przypadku używania puli połączeń ustalony zestaw połączeń jest ustanawiany w czasie uruchamiania i konserwowany. Pozwala to również zmniejszyć fragmentację pamięci na serwerze, który jest spowodowany przez dynamiczne nowe połączenia ustanowione na serwerze bazy danych. Pulę połączeń można skonfigurować po stronie aplikacji, jeśli jest ona obsługiwana przez platformę aplikacji lub sterownik bazy danych. Jeśli to nie jest obsługiwane, inna zalecana opcja polega na wykorzystaniu usługi pulę połączenie serwera proxy, takiej jak [PgBouncer](https://pgbouncer.github.io/) lub [pgpool](https://pgpool.net/mediawiki/index.php/Main_Page) działającej poza aplikacją i nawiązaniu połączenia z serwerem bazy danych. Zarówno PgBouncer, jak i pgpool są narzędziami opartymi na społeczności, które współpracują z Azure Database for PostgreSQL.

### <a name="retry-logic-to-handle-transient-errors"></a>Logika ponawiania w celu obsługi błędów przejściowych
W aplikacji mogą występować błędy przejściowe, w przypadku których połączenia z bazą danych są usuwane lub tracone sporadycznie. W takich sytuacjach serwer działa po jednym do dwóch ponownych prób w ciągu 5 – 10 sekund. Dobrym sposobem jest odczekanie 5 sekund przed pierwszym ponowieniem próby. Następnie postępuj zgodnie z poszczególnymi ponowień, zwiększając czas oczekiwania, aż do 60 sekund. Ogranicz maksymalną liczbę ponownych prób, w których aplikacja uważa, że operacja nie powiodła się, więc możesz kontynuować badanie. Zobacz [, jak rozwiązywać problemy z błędami połączenia](./concepts-connectivity.md) , aby dowiedzieć się więcej.

### <a name="enable-read-replication-to-mitigate-failovers"></a>Włącz replikację odczytu w celu ograniczenia trybu failover
Możesz użyć [replikacja typu Data-in](./concepts-read-replicas.md) w scenariuszach pracy awaryjnej. W przypadku korzystania z replik odczytu nie są wykonywane żadne automatyczne tryb failover między serwerami źródłowym i repliki. Zauważysz zwłokę między źródłem a repliką, ponieważ replikacja jest asynchroniczna. Opóźnienie sieci może mieć wpływ na wiele czynników, takich jak rozmiar obciążenia uruchomionego na serwerze źródłowym i opóźnienie między centrami danych. W większości przypadków opóźnienie replik z kilku sekund do kilku minut.


## <a name="database-deployment"></a>Wdrażanie bazy danych

### <a name="configure-cicd-deployment-pipeline"></a>Konfigurowanie potoku wdrożenia ciągłej integracji/ciągłego wdrażania
Czasami należy wdrożyć zmiany w bazie danych. W takich przypadkach można korzystać z ciągłej integracji (CI) za pomocą [akcji GitHub](https://github.com/Azure/postgresql/blob/master/README.md) dla serwera PostgreSQL, aby zaktualizować bazę danych przez uruchomienie niestandardowego skryptu.

### <a name="define-manual-database-deployment-process"></a>Zdefiniuj proces ręcznego wdrażania bazy danych
Podczas ręcznego wdrażania bazy danych wykonaj następujące kroki, aby zminimalizować przestoje lub ograniczyć ryzyko niepowodzenia wdrożenia:

- Utwórz kopię produkcyjnej bazy danych w nowej bazie danych przy użyciu pg_dump.
- Aktualizowanie nowej bazy danych przy użyciu nowych zmian schematu lub aktualizacji wymaganych dla bazy danych.
- Umieść produkcyjną bazę danych w stanie tylko do odczytu. Nie należy mieć operacji zapisu w produkcyjnej bazie danych, dopóki wdrożenie nie zostanie ukończone.
- Przetestuj swoją aplikację przy użyciu nowo zaktualizowanej bazy danych z kroku 1.
- Wdróż zmiany w aplikacji i upewnij się, że aplikacja korzysta teraz z nowej bazy danych, która ma najnowsze aktualizacje.
- Zachowaj starą produkcyjną bazę danych, aby można było wycofać zmiany. Następnie można oszacować, aby usunąć starą produkcyjną bazę danych lub wyeksportować ją do usługi Azure Storage w razie potrzeby.

>  [!NOTE]
> Jeśli aplikacja jest taka sama jak aplikacja handlu elektronicznego i nie można jej umieścić w stanie tylko do odczytu, należy wdrożyć zmiany bezpośrednio w produkcyjnej bazie danych po utworzeniu kopii zapasowej. Zmiany te powinny być wykonywane poza godzinami szczytu z niskim ruchem do aplikacji w celu zminimalizowania wpływu, ponieważ niektórzy użytkownicy mogą napotkać Nieudane żądania. Upewnij się, że kod aplikacji obsługuje również wszelkie Nieudane żądania.

## <a name="database-schema-and-queries"></a>Schemat bazy danych i zapytania
Poniżej przedstawiono kilka wskazówek, które należy wziąć pod uwagę podczas budowania schematu bazy danych i zapytań.

### <a name="use-bigint-or-uuid-for-primary-keys"></a>Użyj typu BIGINT lub UUID dla kluczy podstawowych
Podczas kompilowania aplikacji niestandardowych lub niektórych struktur, które mogą być używane `INT` zamiast `BIGINT` kluczy podstawowych. W przypadku korzystania z programu ```INT``` należy uruchomić ryzyko, gdzie wartość w bazie danych może przekroczyć pojemność magazynu ```INT``` typu danych. Wprowadzenie tej zmiany do istniejącej aplikacji produkcyjnej może zająć dużo czasu, aby zwiększyć koszt projektowania. Inną opcją jest użycie [identyfikatora UUID](https://www.postgresql.org/docs/current/datatype-uuid.html) dla kluczy podstawowych. Ten identyfikator używa automatycznie generowanego ciągu 128-bitowego, na przykład ```a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11``` . Dowiedz się więcej o [typach danych PostgreSQL](https://www.postgresql.org/docs/8.1/datatype.html).

### <a name="use-indexes"></a>Używanie indeksów

Istnieje wiele typów [indeksów](https://www.postgresql.org/docs/9.1/indexes.html) w Postgres, które mogą być używane na różne sposoby. Użycie indeksu ułatwia serwerowi znalezienie i pobranie określonych wierszy znacznie szybciej niż w przypadku braku indeksu. Jednak indeksy są również dodawane do serwera bazy danych, dlatego Unikaj zbyt wielu indeksów.

### <a name="use-autovacuum"></a>Użyj autopróżni
Możesz zoptymalizować serwer z funkcją autopróżni na serwerze Azure Database for PostgreSQL. PostgreSQL umożliwiają wyższe współbieżność bazy danych, ale przy każdej aktualizacji wyniki operacji INSERT i DELETE. W przypadku usuwania rekordy są oznaczane niewygładzone, które zostaną później oczyszczone. Aby wykonać te zadania, PostgreSQL uruchamia zadanie próżniowe. W przypadku braku próżni od czasu do czasu, niedziałające krotki, które gromadzą się mogą spowodować:

- Przeładowanie danych, takie jak większe bazy danych i tabele.
- Większe nieoptymalne indeksy.
- Zwiększona we/wy.

Dowiedz się więcej o tym [, jak zoptymalizować za pomocą autopróżni](howto-optimize-autovacuum.md).

### <a name="use-pg_stats_statements"></a>Użyj pg_stats_statements
Pg_stat_statements to rozszerzenie PostgreSQL, które jest domyślnie włączone w Azure Database for PostgreSQL. Rozszerzenie zapewnia metodę śledzenia statystyk wykonywania dla wszystkich instrukcji SQL wykonywanych przez serwer. Zobacz [, jak używać pg_statement](howto-optimize-query-stats-collection.md).


### <a name="use-the-query-store"></a>Korzystanie z magazynu zapytań
Funkcja [magazynu zapytań](./concepts-query-store.md) w Azure Database for PostgreSQL zapewnia bardziej efektywną metodę śledzenia statystyk zapytań. Firma Microsoft zaleca korzystanie z tej funkcji jako alternatywy w odniesieniu do pg_stats_statements.

### <a name="optimize-bulk-inserts-and-use-transient-data"></a>Optymalizacja zbiorczych operacji wstawiania i używania danych przejściowych
Jeśli istnieją operacje związane z obciążeniem, które obejmują dane przejściowe lub wstawiają duże zbiory danych zbiorczo, należy rozważyć użycie niezarejestrowanych tabel. Domyślnie zapewnia ona niepodzielność i trwałość. Wartość niepodzielności, spójności, izolacji i trwałości tworzą właściwości KWAŚNe. Zobacz [, jak zoptymalizować Wstawianie zbiorcze](howto-optimize-bulk-inserts.md).

## <a name="next-steps"></a>Następne kroki
[Przewodnik Postgres](http://postgresguide.com/)
