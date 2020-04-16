---
title: Przydziały usługi Usługi Azure Cosmos DB
description: Przydziały usługi usługi Usługi Azure Cosmos DB i domyślne limity dla różnych typów zasobów.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: b24d7db679bb9cb9dacd5e1db8e6410b883548cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415705"
---
# <a name="azure-cosmos-db-service-quotas"></a>Przydziały usługi Usługi Azure Cosmos DB

Ten artykuł zawiera omówienie domyślnych przydziałów oferowanych na potrzeby różnych zasobów w usłudze Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Pamięć masowa i przepustowość

Po utworzeniu konta usługi Azure Cosmos w ramach subskrypcji można zarządzać danymi na koncie, [tworząc bazy danych, kontenery i elementy](databases-containers-items.md). Można aprowizować przepływność na poziomie kontenera lub na poziomie bazy danych pod względem [jednostek żądań (RU/s lub RU)](request-units.md). W poniższej tabeli wymieniono limity magazynowania i przepływności na kontener/bazę danych.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna ilość energii na kontener[(tryb aprowizacji przepływności dedykowanej)](databases-containers-items.md#azure-cosmos-containers) | domyślnie 1 000 000. Możesz go zwiększyć, [składając bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Maksymalna liczba procesorów RUs na bazę danych[(tryb aprowizacji przepływociądziowej udostępnionej)](databases-containers-items.md#azure-cosmos-containers) | domyślnie 1 000 000. Możesz go zwiększyć, [składając bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Maksymalna liczba procesorów na (logiczny) klucz partycji | 10 000 |
| Maksymalna ilość miejsca do magazynowania wszystkich elementów na (logiczny) klucz partycji| 20 GB |
| Maksymalna liczba odrębnych (logicznych) kluczy partycji | Nieograniczona liczba |
| Maksymalna ilość miejsca przechowywania na kontener | Nieograniczona liczba |
| Maksymalna ilość miejsca na bazę danych | Nieograniczona liczba |
| Maksymalny rozmiar załącznika na konto (funkcja załącznika jest amortyzowana) | 2 GB |
| Minimalna ilość procesorów r./ wymagana na 1 GB | 10 ru/s |

> [!NOTE]
> Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących zarządzania obciążeniami, które mają klucze partycji wymagające wyższych limitów przechowywania lub przepływności, zobacz [Tworzenie syntetycznego klucza partycji](synthetic-partition-keys.md).
>

Kontener usługi Cosmos (lub udostępnionej bazy danych przepływności) musi mieć minimalną przepływność 400 procesorów RU. Wraz z rozwojem kontenera minimalna obsługiwana przepustowość zależy również od następujących czynników:

* Minimalna przepływność, którą można ustawić w kontenerze, zależy od maksymalnej przepływności, jaką kiedykolwiek zostały zainicjowane w kontenerze. Na przykład jeśli przepustowość została zwiększona do 10000 rUs, a następnie najniższą możliwą aprowizowaną przepływność będzie 1000 RUs
* Minimalna przepływność w bazie danych przepływności udostępnionej zależy również od całkowitej liczby kontenerów, które kiedykolwiek utworzono w bazie danych przepływności udostępnionej, mierzonej na 100 procesorach RUs na kontener. Na przykład jeśli utworzono pięć kontenerów w bazie danych przepływności udostępnionej, przepływność musi wynosić co najmniej 500 procesorów RU

Bieżąca i minimalna przepływność kontenera lub bazy danych można pobrać z witryny Azure portal lub zestawów SDK. Aby uzyskać więcej informacji, zobacz [Aprowizuj przepływność kontenerów i baz danych](set-throughput.md). 

> [!NOTE]
> W niektórych przypadkach można obniżyć przepustowość do mniej niż 10%. Użyj interfejsu API, aby uzyskać dokładne minimalne procesory RUs na kontener.
>

Podsumowując, oto minimalne aprowizowane limity RU. 

| Zasób | Limit domyślny |
| --- | --- |
| Minimalna ilość energii na kontener[(tryb aprowizacji przepływności dedykowanej)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| Minimalna ilość energii na bazę danych[(tryb aprowizacji przepływociądzia współużytkowana)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| Minimalna liczba procesorów RU na kontener w udostępnionej bazie danych przepływności | 100 |

Usługa Cosmos DB obsługuje elastyczne skalowanie przepływności (RUs) na kontener lub bazę danych za pośrednictwem zestawów SDK lub portalu. Każdy kontener można skalować synchronicznie i natychmiast w zakresie skali od 10 do 100 razy, między wartościami minimalnymi i maksymalnymi. Jeśli żądana wartość przepływności znajduje się poza zakresem, skalowanie jest wykonywane asynchronicznie. Skalowanie asynchroniczne może potrwać od minut do godzin, aby zakończyć w zależności od żądanej przepływności i rozmiaru magazynu danych w kontenerze.  

## <a name="control-plane-operations"></a>Operacje płaszczyzny sterowania

Konto [usługi Azure Cosmos można aprowizować i zarządzać nim](how-to-manage-database-account.md) przy użyciu szablonów usługi Azure portal, Azure PowerShell, Azure CLI i Azure Resource Manager. W poniższej tabeli wymieniono limity na subskrypcję, konto i liczbę operacji.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna liczba kont bazy danych na subskrypcję | 50 domyślnie. Możesz go zwiększyć, [składając bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Maksymalna liczba regionalnych awarii | domyślnie 1/godz. Możesz go zwiększyć, [składając bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|

> [!NOTE]
> Regionalne tryb failovers dotyczą tylko kont zapisu w jednym regionie. Konta zapisu w wielu regionach nie wymagają ani nie mają żadnych ograniczeń dotyczących zmiany regionu zapisu.

Usługa Cosmos DB automatycznie wykonuje kopie zapasowe danych w regularnych odstępach czasu. Aby uzyskać szczegółowe informacje na temat interwałów przechowywania kopii zapasowych i okien, zobacz [Tworzenie kopii zapasowych w trybie online i przywracanie danych na żądanie w usłudze Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Limity na konto

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna liczba baz danych | Nieograniczona liczba |
| Maksymalna liczba kontenerów na bazę danych z współdzieloną przepływnością |25 |
| Maksymalna liczba kontenerów na bazę danych lub konto z dedykowaną przepustowością  |bez ograniczeń |
| Maksymalna liczba regionów | Brak limitu (wszystkie regiony platformy Azure) |

## <a name="per-container-limits"></a>Limity dla kontenerów

W zależności od używanego interfejsu API kontener usługi Azure Cosmos może reprezentować kolekcję, tabelę lub wykres. Kontenery obsługują konfiguracje [unikatowych ograniczeń klucza,](unique-keys.md) [procedur przechowywanych, wyzwalaczy i plików UDF](stored-procedures-triggers-udfs.md)oraz [zasad indeksowania.](how-to-manage-indexing-policy.md) W poniższej tabeli wymieniono limity specyficzne dla konfiguracji w kontenerze. 

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna długość nazwy bazy danych lub kontenera | 255 |
| Maksymalna liczba procedur przechowywanych w pojemniku | 100<sup>*</sup>|
| Maksymalna liczba plików UDF na kontener | 25<sup>*</sup>|
| Maksymalna liczba ścieżek w zasadach indeksowania| 100<sup>*</sup>|
| Maksymalna liczba unikatowych kluczy w kontenerze|10<sup>*</sup>|
| Maksymalna liczba ścieżek na unikatowe ograniczenie klucza|16<sup>*</sup>|

<sup>*</sup>Możesz zwiększyć dowolne z tych limitów dla kontenera, kontaktując się z pomocą techniczną platformy Azure.

## <a name="per-item-limits"></a>Limity na towar

W zależności od używanego interfejsu API element usługi Azure Cosmos może reprezentować dokument w kolekcji, wiersz w tabeli lub węzeł lub krawędź na wykresie. W poniższej tabeli przedstawiono limity dla elementu w usłudze Cosmos DB. 

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalny rozmiar towaru | 2 MB (długość UTF-8 reprezentacji JSON) |
| Maksymalna długość wartości klucza partycji | 2048 bajtów |
| Maksymalna długość wartości identyfikatora | 1023 bajtów |
| Maksymalna liczba właściwości na element | Brak praktycznych ograniczeń |
| Maksymalna głębokość zagnieżdżenia | Brak praktycznych ograniczeń |
| Maksymalna długość nazwy właściwości | Brak praktycznych ograniczeń |
| Maksymalna długość wartości właściwości | Brak praktycznych ograniczeń |
| Maksymalna długość wartości właściwości string | Brak praktycznych ograniczeń |
| Maksymalna długość wartości właściwości liczbowej | IEEE754 podwójna precyzja 64-bitowa |

Nie ma żadnych ograniczeń dotyczących ładunków elementów, takich jak liczba właściwości i głębokość zagnieżdżania, z wyjątkiem ograniczeń długości dla wartości klucza partycji i identyfikatora oraz ogólnego ograniczenia rozmiaru 2 MB. Może być trzeba skonfigurować zasady indeksowania dla kontenerów z dużych lub złożonych struktur towarów w celu zmniejszenia zużycia ru. Zobacz [modelowanie elementów w usłudze Cosmos DB,](how-to-model-partition-example.md) aby uzyskać przykład w świecie rzeczywistym, a wzorce do zarządzania dużymi elementami.

## <a name="per-request-limits"></a>Limity na żądanie

Usługa Azure Cosmos DB obsługuje [operacje CRUD i zapytań](https://docs.microsoft.com/rest/api/cosmos-db/) względem zasobów, takich jak kontenery, elementy i bazy danych. Obsługuje również [transakcyjne żądania partii](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) dla wielu elementów z tym samym kluczem partycji w kontenerze.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalny czas wykonywania dla pojedynczej operacji (np. wykonanie procedury składowanej lub pobieranie strony pojedynczej kwerendy)| 5 sek. |
| Maksymalny rozmiar żądania (na przykład procedura składowana, CRUD)| 2 MB |
| Maksymalny rozmiar odpowiedzi (na przykład kwerenda na strony) | 4 MB |
| Maksymalna liczba operacji w partii transakcyjnej | 100 |

Once an operation like query reaches the execution timeout or response size limit, it returns a page of results and a continuation token to the client to resume execution. Nie ma praktycznego limitu czasu trwania pojedynczego zapytania można uruchomić na stronach/kontynuacji.

Usługa Cosmos DB używa HMAC do autoryzacji. Można użyć klucza głównego lub [tokenów zasobów](secure-access-to-data.md) do precyzyjnej kontroli dostępu do zasobów, takich jak kontenery, klucze partycji lub elementy. W poniższej tabeli wymieniono limity tokenów autoryzacji w usłudze Cosmos DB.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalny czas wygaśnięcia tokenu głównego | 15 min.  |
| Minimalny czas wygaśnięcia tokenu zasobu | 10 min.  |
| Maksymalny czas wygaśnięcia tokenu zasobu | 24 h domyślnie. Możesz go zwiększyć, [składając bilet pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Maksymalne pochylenie zegara dla autoryzacji tokenu| 15 min. |

Usługa Cosmos DB obsługuje wykonywanie wyzwalaczy podczas zapisów. Usługa obsługuje maksymalnie jeden pre-trigger i jeden post-trigger na operację zapisu. 

## <a name="autopilot-mode-limits"></a>Limity trybu autopilota

Zobacz artykuł [autopilota,](provision-throughput-autopilot.md#autopilot-limits) aby dostrzec limity przepustowości i pamięci masowej w trybie autopilota.

## <a name="sql-query-limits"></a>Limity zapytań SQL

Usługa Cosmos DB obsługuje wykonywanie zapytań o elementy przy użyciu programu [SQL](how-to-sql-query.md). W poniższej tabeli opisano ograniczenia w instrukcjach kwerendy, na przykład pod względem liczby klauzul lub długości kwerendy.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna długość kwerendy SQL| 256 KB |
| Maksymalna liczba jonów na kwerendę| 5<sup>*</sup>|
| Maksymalna liczba plików UDF na kwerendę| 10<sup>*</sup>|
| Maksymalna liczba punktów na wielokąt| 4096 |
| Maksymalna liczba uwzględnionych ścieżek na kontener| 500 |
| Maksymalna wykluczona ścieżka na kontener| 500 |
| Maksymalne właściwości w indeksie złożonym| 8 |

<sup>*</sup>Te limity zapytań SQL można zwiększyć, kontaktując się z pomocą techniczną platformy Azure.

## <a name="mongodb-api-specific-limits"></a>Limity specyficzne dla interfejsu API MongoDB

Cosmos DB obsługuje protokół drutu MongoDB dla aplikacji napisanych przeciwko MongoDB. Obsługiwane polecenia i wersje protokołów można znaleźć w [obsługiwanych funkcjach i składni MongoDB](mongodb-feature-support.md).

W poniższej tabeli wymieniono limity specyficzne dla obsługi funkcji MongoDB. Inne limity usług wymienione dla interfejsu API SQL (core) dotyczą również interfejsu API MongoDB.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalny rozmiar pamięci kwerendy MongoDB (to ograniczenie dotyczy tylko wersji serwera 3.2) | 40 MB |
| Maksymalny czas wykonania operacji MongoDB| Lata 30. |
| Limit czasu połączenia bezczynnego dla zamknięcia połączenia po stronie serwera* | 30 minut |

\*Zaleca się, aby aplikacje klienckie ustawiały limit czasu bezczynności połączenia w ustawieniach sterownika na 2-3 minuty, ponieważ [domyślny limit czasu dla usługi Azure LoadBalancer wynosi 4 minuty.](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)  Ten limit czasu zapewni, że bezczynne połączenia nie są zamykane przez pośredni moduł równoważenia obciążenia między komputerem klienckim a usługą Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Wypróbuj limity Cosmos DB Free

W poniższej tabeli wymieniono limity bezpłatnej wersji próbnej [usługi Try Azure Cosmos DB.](https://azure.microsoft.com/try/cosmosdb/)

| Zasób | Limit domyślny |
| --- | --- |
| Czas trwania badania | 30 dni (można odnowić dowolną liczbę razy) |
| Maksymalna liczba kontenerów na subskrypcję (SQL, Gremlin, Table API) | 1 |
| Maksymalna liczba kontenerów na subskrypcję (interfejs API MongoDB) | 3 |
| Maksymalna przepustowość na kontener | 5000 |
| Maksymalna przepustowość na bazę danych współdzielonej przepływności | 20000 |
| Maksymalna całkowita ilość miejsca na koncie | 10 GB |

Wypróbuj usługę Cosmos DB obsługuje globalną dystrybucję tylko w regionach Środkowe stany USA, Europa Północna i Azja Południowo-Wschodnia. Nie można utworzyć biletów pomocy technicznej platformy Azure dla kont usługi Try Usługi Azure Cosmos DB. Jednak pomoc techniczna jest dostępna dla subskrybentów z istniejącymi planami pomocy technicznej.

## <a name="free-tier-account-limits"></a>Limity kont warstwy bezpłatnej
W poniższej tabeli wymieniono limity dla [kont warstw bezpłatnych usługi Azure Cosmos DB.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Zasób | Limit domyślny |
| --- | --- |
| Liczba kont warstw bezpłatnych na subskrypcję platformy Azure | 1 |
| Czas trwania rabatu na poziomie wolnym | Okres istnienia konta. Musi wyrazić zgodę podczas tworzenia konta. |
| Maksymalna ilość RU/s za darmo | 400 RU/s |
| Maksymalna ilość miejsca do swobodnego przechowywania | 5 GB |
| Maksymalna liczba udostępnionych baz danych przepływności | 5 |
| Maksymalna liczba kontenerów w udostępnionej bazie danych przepływności | 25 <br>Na kontach warstwy bezpłatnej minimalna wartość RU/s dla udostępnionej bazy danych przepływności z maksymalnie 25 kontenerami wynosi 400 serwerów RU/s. |

  Oprócz powyższych [limitów na konto](#per-account-limits) mają zastosowanie również do kont warstw bezpłatnych.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o podstawowych pojęciach usługi Cosmos DB [globalna dystrybucja](distribute-data-globally.md) i [partycjonowanie](partitioning-overview.md) oraz [aprowizowana przepływność](request-units.md).

Rozpocznij pracę z usługą Azure Cosmos DB, korzystając z jednego z naszych przewodników:

* [Rozpoczynanie pracy z interfejsem SQL usługi Azure Cosmos DB](create-sql-api-dotnet.md)
* [Rozpoczynanie pracy z interfejsem MongoDB usługi Azure Cosmos DB](create-mongodb-nodejs.md)
* [Rozpoczynanie pracy z interfejsem API rozwiązania Cassandra usługi Azure Cosmos DB](create-cassandra-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API programu Gremlin usługi Azure Cosmos DB](create-graph-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API tabeli usługi Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Wypróbuj usługę Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/)
