---
title: Przydziały usługi Azure Cosmos DB
description: Azure Cosmos DB przydziały usługi i domyślne limity dla różnych typów zasobów.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 9ace9a319f4cc6bcc1545d6d1becce61b1892765
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598675"
---
# <a name="azure-cosmos-db-service-quotas"></a>Przydziały usługi Azure Cosmos DB

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Ten artykuł zawiera omówienie domyślnych przydziałów oferowanych na potrzeby różnych zasobów w usłudze Azure Cosmos DB.

## <a name="storage-and-database-operations"></a>Operacje magazynu i bazy danych

Po utworzeniu konta usługi Azure Cosmos w ramach subskrypcji możesz zarządzać danymi na swoim koncie przez [Tworzenie baz danych, kontenerów i elementów](account-databases-containers-items.md).

### <a name="provisioned-throughput"></a>Aprowizowana przepływność

Przepustowość można zainicjować na poziomie kontenera lub na poziomie bazy danych w warunkach [jednostek żądania (ru/s lub jednostek ru)](request-units.md). W poniższej tabeli wymieniono limity dotyczące magazynu i przepływności dla kontenera/bazy danych.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna jednostek ru na kontener ([tryb aprowizacji dedykowanej przepustowości](account-databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 domyślnie. Możesz ją zwiększyć, [bilet pomocy technicznej platformy Azure](create-support-request-quota-increase.md) |
| Maksymalna liczba jednostek ru na bazę danych ([tryb aprowizacji udostępnionej przepustowości](account-databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 domyślnie. Możesz ją zwiększyć, [bilet pomocy technicznej platformy Azure](create-support-request-quota-increase.md) |
| Maksymalna jednostek ru na (logiczne) partycji | 10 000 |
| Maksymalny rozmiar magazynu na partycję wszystkie elementy na (logiczne) | 20 GB |
| Maksymalna liczba różnych (logicznych) kluczy partycji | Nieograniczona liczba |
| Maksymalny rozmiar magazynu na kontener | Nieograniczona liczba |
| Maksymalny rozmiar magazynu na bazę danych | Nieograniczona liczba |
| Maksymalny rozmiar załączników na konto (funkcja załącznika jest przestarzała) | 2 GB |
| Minimalna wymagana jednostka RU/s na 1 GB | 10 RU/s<br>**Uwaga:** to minimum można obniżyć, jeśli Twoje konto jest uprawnione do naszego [programu "High Storage/niska przepływność"](set-throughput.md#high-storage-low-throughput-program) |

> [!NOTE]
> Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących zarządzania obciążeniami z kluczami partycji wymagającymi wyższych limitów dla magazynu lub przepływności, zobacz [Tworzenie klucza partycji syntetycznej](synthetic-partition-keys.md).

### <a name="minimum-throughput-limits"></a>Minimalne limity przepływności

Kontener Cosmos (lub udostępniona baza danych przepływności) musi mieć minimalną przepływność wynoszącą 400 RU/s. W miarę wzrostu kontenera Cosmos DB wymaga minimalnej przepływności, aby upewnić się, że baza danych lub kontener ma wystarczający zasób do operacji.

Bieżącą i minimalną przepływność kontenera lub bazy danych można pobrać z Azure Portal lub zestawów SDK. Aby uzyskać więcej informacji, zobacz temat [udostępnianie przepływności na kontenerach i bazach danych](set-throughput.md). 

Rzeczywiste minimum RU/s może się różnić w zależności od konfiguracji konta. Za pomocą [metryk Azure monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) można wyświetlić historię zainicjowanej przepływności (ru/s) i magazynu w ramach zasobu. 

#### <a name="minimum-throughput-on-container"></a>Minimalna przepływność na kontenerze 

Aby oszacować minimalną przepływność wymaganą przez kontener z ręczną przepływem, Znajdź maksymalnie:

* 400 RU/s 
* Bieżący magazyn w GB * 10 RU/s
* Najwyższy poziom RU/s zainicjowany w kontenerze/100

Przykład: Załóżmy, że masz kontener z obsługą 400 RU/s i 0 GB magazynu. Można zwiększyć przepływność do 50 000 RU/s i zaimportować 20 GB danych. Minimalna wartość RU/s to teraz `MAX(400, 20 * 10 RU/s per GB, 50,000 RU/s / 100)` = 500 ru/s. W miarę upływu czasu magazyn rośnie do 200 GB. Minimalna wartość RU/s to teraz `MAX(400, 200 * 10 RU/s per GB, 50,000 / 100)` = 2000 ru/s. 

**Uwaga:** minimalny przepływność 10 jednostek ru/s na GB magazynu można obniżyć, jeśli Twoje konto kwalifikuje się do korzystania z naszego [programu "High Storage/niska przepływność"](set-throughput.md#high-storage-low-throughput-program).

#### <a name="minimum-throughput-on-shared-throughput-database"></a>Minimalna przepływność dla udostępnionej bazy danych przepływności 
Aby oszacować minimalną przepływność wymaganą przez udostępnioną bazę danych przepływności z ręczną przepływność, Znajdź maksymalnie:

* 400 RU/s 
* Bieżący magazyn w GB * 10 RU/s
* Najwyższy poziom RU/s zainicjowany w bazie danych/100
* 400 + MAX (liczba kontenerów-25, 0) * 100 RU/s

Przykład: Załóżmy, że masz bazę danych z obsługą 400 RU/s, 15 GB miejsca do magazynowania i 10 kontenerów. Minimalna wartość RU/s to `MAX(400, 15 * 10 RU/s per GB, 400 / 100, 400 + 0 )` = 400 ru/s. Jeśli w bazie danych wystąpiło 30 kontenerów, minimalna wartość RU/s byłaby `400 + MAX(30 - 25, 0) * 100 RU/s` = 900 ru/s. 

**Uwaga:** minimalny przepływność 10 jednostek ru/s na GB magazynu można obniżyć, jeśli Twoje konto kwalifikuje się do korzystania z naszego [programu "High Storage/niska przepływność"](set-throughput.md#high-storage-low-throughput-program).

Poniżej znajduje się podsumowanie minimalnych limitów przydziałów RU. 

| Zasób | Limit domyślny |
| --- | --- |
| Minimalna jednostek ru na kontener ([tryb aprowizacji dedykowanej przepustowości](./account-databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimalna jednostek ru na bazę danych ([tryb aprowizacji udostępnionej przepustowości](./account-databases-containers-items.md#azure-cosmos-containers)) | 400 RU/s dla pierwszych 25 kontenerów. Dodatkowe 100 RU/s dla każdego kontenera później. |

Cosmos DB obsługuje programistyczne skalowanie przepływności (RU/s) na kontener lub bazę danych za pośrednictwem zestawów SDK lub portalu.    

W zależności od bieżących zasobów i ustawień zainicjowanych przez program RU/s każdy zasób można skalować synchronicznie i od razu między minimalnymi jednostkami RU/s do maksymalnie 100x minimalnych jednostek RU/s. Jeśli żądana wartość przepływności znajduje się poza zakresem, skalowanie jest wykonywane asynchronicznie. Skalowanie asynchroniczne może zająć kilka minut, w zależności od wymaganej przepływności i rozmiaru magazynu danych w kontenerze.  

### <a name="serverless"></a>Praca bezserwerowa

[Bezserwerowe](serverless.md) pozwala używać zasobów Azure Cosmos DB w sposób oparty na użyciu. W poniższej tabeli przedstawiono limity dotyczące przestrzeni dyskowej i przepływności dla kontenera/bazy danych.

| Zasób | Limit |
| --- | --- |
| Maksymalna liczba jednostek RU na sekundę (logiczne) | 5000 |
| Maksymalny rozmiar magazynu na partycję wszystkie elementy na (logiczne) | 20 GB |
| Maksymalna liczba różnych (logicznych) kluczy partycji | Nieograniczona liczba |
| Maksymalny rozmiar magazynu na kontener | 50 GB |

## <a name="control-plane-operations"></a>Operacje płaszczyzny kontroli

Możesz [zainicjować konto usługi Azure Cosmos i zarządzać](how-to-manage-database-account.md) nim przy użyciu szablonów Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy azure i Azure Resource Manager. W poniższej tabeli wymieniono limity dla każdej subskrypcji, konta i liczby operacji.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna liczba kont bazy danych na subskrypcję | 50 domyślnie. Możesz ją zwiększyć, [bilet pomocy technicznej platformy Azure](create-support-request-quota-increase.md)|
| Maksymalna liczba regionalnych trybu failover | Domyślnie 1 godzinę. Możesz ją zwiększyć, [bilet pomocy technicznej platformy Azure](create-support-request-quota-increase.md)|

> [!NOTE]
> Regionalne przejścia w tryb failover mają zastosowanie tylko do jednego regionu zapisy konta. Konta zapisu w regionie wieloregionowym nie wymagają ani nie mają ograniczeń dotyczących zmiany regionu zapisu.

Cosmos DB automatycznie pobiera kopie zapasowe danych w regularnych odstępach czasu. Aby uzyskać szczegółowe informacje na temat interwałów przechowywania i okien kopii zapasowych, zobacz [kopia zapasowa online i przywracanie danych na żądanie w Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Limity dla konta

### <a name="provisioned-throughput"></a>Aprowizowana przepływność

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna liczba baz danych | Nieograniczona liczba |
| Maksymalna liczba kontenerów na bazę danych o udostępnionej przepływności |25 |
| Maksymalna liczba kontenerów na bazę danych lub konto z dedykowaną przepływność  |bez ograniczeń |
| Maksymalna liczba regionów | Brak limitu (wszystkie regiony platformy Azure) |

### <a name="serverless"></a>Praca bezserwerowa

| Zasób | Limit |
| --- | --- |
| Maksymalna liczba baz danych | Nieograniczona liczba |
| Maksymalna liczba kontenerów na konto  | 100 |
| Maksymalna liczba regionów | 1 (dowolny region świadczenia usługi Azure) |

## <a name="per-container-limits"></a>Limity dla kontenerów

W zależności od używanego interfejsu API kontener usługi Azure Cosmos może reprezentować kolekcję, tabelę lub wykres. Kontenery obsługują konfiguracje dla [ograniczeń unique Key](unique-keys.md), [procedur składowanych, wyzwalaczy i UDF](stored-procedures-triggers-udfs.md)oraz [zasad indeksowania](how-to-manage-indexing-policy.md). W poniższej tabeli wymieniono limity specyficzne dla konfiguracji w kontenerze. 

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna długość nazwy bazy danych lub kontenera | 255 |
| Maksymalna liczba procedur składowanych na kontener | 100 <sup>*</sup>|
| Maksymalna UDF na kontener | 6,25 <sup>*</sup>|
| Maksymalna liczba ścieżek w zasadach indeksowania| 100 <sup>*</sup>|
| Maksymalna liczba unikatowych kluczy na kontener|dziesięć <sup>*</sup>|
| Maksymalna liczba ścieżek na ograniczenie unikatowego klucza|16 <sup>*</sup>|
| Maksymalna wartość czasu wygaśnięcia |2147483647|

<sup>*</sup> Można zwiększyć dowolne z tych limitów kontenerów, tworząc [support Request platformy Azure](create-support-request-quota-increase.md).

## <a name="per-item-limits"></a>Limity dla każdego elementu

W zależności od używanego interfejsu API element platformy Azure Cosmos może reprezentować dokument w kolekcji, wiersz w tabeli lub węzeł lub krawędź wykresu. W poniższej tabeli przedstawiono limity dla każdego elementu w Cosmos DB. 

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalny rozmiar elementu | 2 MB (długość UTF-8 w reprezentacji JSON) |
| Maksymalna długość wartości klucza partycji | 2048 bajtów |
| Maksymalna długość wartości identyfikatora | 1023 bajtów |
| Maksymalna liczba właściwości na element | Brak praktycznego limitu |
| Maksymalna długość nazwy właściwości | Brak praktycznego limitu |
| Maksymalna długość wartości właściwości | Brak praktycznego limitu |
| Maksymalna długość wartości właściwości ciągu | Brak praktycznego limitu |
| Maksymalna długość wartości właściwości liczbowej | IEEE754 o podwójnej precyzji 64-bitowej |
| Maksymalny poziom zagnieżdżenia dla osadzonych obiektów/tablic | 128 |
| Maksymalna wartość czasu wygaśnięcia |2147483647|

Nie ma żadnych ograniczeń dotyczących ładunków elementów, takich jak liczba właściwości i głębokość zagnieżdżenia, z wyjątkiem ograniczeń długości klucza partycji i wartości identyfikatora oraz całkowitego ograniczenia rozmiaru wynoszącego 2 MB. Może być konieczne skonfigurowanie zasad indeksowania dla kontenerów z dużymi lub złożonymi strukturami elementów w celu ograniczenia zużycia RU. Zobacz [modelowanie elementów w Cosmos DB](how-to-model-partition-example.md) , aby zapoznać się z rzeczywistym przykładem i wzorcem do zarządzania dużymi elementami.

## <a name="per-request-limits"></a>Limity żądań na żądanie

Azure Cosmos DB obsługuje [operacje CRUD i zapytań](/rest/api/cosmos-db/) wobec zasobów, takich jak kontenery, elementy i bazy danych. Obsługuje ona również [transakcyjne żądania wsadowe](/dotnet/api/microsoft.azure.cosmos.transactionalbatch) dla wielu elementów z tym samym kluczem partycji w kontenerze.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalny czas wykonywania pojedynczej operacji (na przykład wykonanie procedury składowanej lub pobranie pojedynczej strony zapytania)| 5 sekund |
| Maksymalny rozmiar żądania (na przykład procedura składowana, CRUD)| 2 MB |
| Maksymalny rozmiar odpowiedzi (na przykład zapytanie z podziałem na strony) | 4 MB |
| Maksymalna liczba operacji w partii transakcyjnej | 100 |

Gdy operacja, taka jak zapytanie osiągnie limit czasu wykonania lub wartość ograniczenia rozmiaru, zwraca stronę wyników i token kontynuacji klientowi, aby wznowić wykonywanie. Nie ma praktycznego limitu czasu trwania pojedynczego zapytania, które można uruchomić na stronach/kontynuacji.

Cosmos DB używa algorytmu HMAC do autoryzacji. Można użyć klucza podstawowego lub [tokenów zasobów](secure-access-to-data.md) do precyzyjnej kontroli dostępu do zasobów, takich jak kontenery, klucze partycji lub elementy. W poniższej tabeli wymieniono limity tokenów autoryzacji w Cosmos DB.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalny czas wygaśnięcia tokenu podstawowego | 15 minut  |
| Minimalny czas wygaśnięcia tokenu zasobów | 10 min  |
| Maksymalny czas wygaśnięcia tokenu zasobu | Domyślnie 24 h. Możesz ją zwiększyć, [bilet pomocy technicznej platformy Azure](create-support-request-quota-increase.md)|
| Maksymalne przechylenie zegara na potrzeby autoryzacji tokenu| 15 minut |

Cosmos DB obsługuje wykonywanie wyzwalaczy podczas operacji zapisu. Usługa obsługuje maksymalnie jednego wyzwalacza wstępnego i jednego wyzwalacza końcowego dla operacji zapisu.

## <a name="metadata-request-limits"></a>Limity żądań metadanych

Azure Cosmos DB przechowuje metadane systemowe dla każdego konta. Te metadane umożliwiają Wyliczenie nieodpłatnie kolekcji, baz danych, innych zasobów Azure Cosmos DB i ich konfiguracji.

| Zasób | Limit domyślny |
| --- | --- |
|Maksymalna stawka tworzenia kolekcji na minutę|    100|
|Maksymalna stawka tworzenia bazy danych na minutę|    100|
|Maksymalna liczba zainicjowanych przepływności na minutę|    5|

## <a name="limits-for-autoscale-provisioned-throughput"></a>Limity przepływności aprowizacji automatycznego skalowania

Zapoznaj się z artykułem [skalowania automatycznego](provision-throughput-autoscale.md#autoscale-limits) i [często zadawanych pytań](autoscale-faq.md#lowering-the-max-rus) , aby uzyskać bardziej szczegółowy opis ograniczeń przepływności i magazynowania przy użyciu automatycznego skalowania.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna liczba RU/s, do których można skalować system |  `Tmax`, maksymalne skalowanie RU/s ustawione przez użytkownika|
| Minimalna wartość RU/s, do której można skalować system | `0.1 * Tmax`|
| Bieżący RU/s, do których system jest skalowany  |  `0.1*Tmax <= T <= Tmax`na podstawie użycia|
| Minimalnie obciążane RU/s na godzinę| `0.1 * Tmax` <br></br>Opłaty są naliczane w oparciu o godzinę, gdzie jest naliczana za najwyższy poziom RU/s systemu w ciągu godziny, lub `0.1*Tmax` , w zależności od tego, czy jest wyższy. |
| Minimalne maksymalne skalowanie RU/s dla kontenera  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` zaokrąglony do najbliższej 1000 RU/s |
| Minimalne maksymalne skalowanie RU/s dla bazy danych  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`, zaokrąglony do najbliższej 1000 RU/s. <br></br>Zwróć uwagę, że baza danych ma więcej niż 25 kontenerów, system zwiększa minimalną wartość maksymalnego skalowania automatycznego (RU/s) przez 1000 RU/s dla dodatkowego kontenera. Na przykład jeśli masz 30 kontenerów, najniższe maksymalne skalowanie RU/s, które można ustawić, to 9000 RU/s (skaluje się między 900 9000 RU/s).

## <a name="sql-query-limits"></a>Limity zapytań SQL

Cosmos DB obsługuje wykonywanie zapytań względem elementów przy użyciu [języka SQL](./sql-query-getting-started.md). W poniższej tabeli opisano ograniczenia dotyczące instrukcji zapytania, na przykład w przypadku liczby klauzul lub długości zapytań.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna długość zapytania SQL| 256 KB |
| Maksymalna liczba sprzężeń na zapytanie| 5000 <sup>*</sup>|
| Maksymalna liczba UDF na zapytanie| dziesięć <sup>*</sup>|
| Maksymalna liczba punktów na wielokąt| 4096 |
| Maksymalna liczba uwzględnionych ścieżek na kontener| 500 |
| Maksymalna liczba wykluczonych ścieżek na kontener| 500 |
| Maksymalna liczba właściwości w indeksie złożonym| 8 |

<sup>*</sup> Możesz zwiększyć dowolny z tych limitów zapytań SQL, tworząc [support Request platformy Azure](create-support-request-quota-increase.md).

## <a name="mongodb-api-specific-limits"></a>Limity specyficzne dla interfejsu API MongoDB

Cosmos DB obsługuje protokół sieci szkieletowej MongoDB dla aplikacji, które są zapisywane w MongoDB. Obsługiwane polecenia i wersje protokołu można znaleźć w [obsługiwanych funkcjach i składni MongoDB](mongodb-feature-support.md).

W poniższej tabeli wymieniono limity dotyczące obsługi funkcji MongoDB. Inne limity usług wymienione dla interfejsu API języka SQL (rdzeń) również mają zastosowanie do interfejsu API MongoDB.

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalny rozmiar pamięci zapytania MongoDB (to ograniczenie dotyczy tylko wersji 3,2 serwera) | 40 MB |
| Maksymalny czas wykonywania operacji MongoDB| 30 s |
| Limit czasu bezczynności połączenia dla zamknięcia połączenia po stronie serwera * | 30 minut |

\* Zalecamy, aby aplikacje klienckie ustawili limit czasu bezczynności połączenia w ustawieniach sterownika na 2-3 minut, ponieważ [domyślny limit czasu dla modułu równoważenia obciążenia Azure to 4 minuty](../load-balancer/load-balancer-tcp-idle-timeout.md).  Ten limit czasu pozwala zagwarantować, że bezczynne połączenia nie zostaną zamknięte przez pośredni moduł równoważenia obciążenia między komputerem klienckim a Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Wypróbuj Cosmos DB limitów bezpłatnych

W poniższej tabeli przedstawiono limity dla [Azure Cosmos DB Wypróbuj bezpłatną](https://azure.microsoft.com/try/cosmosdb/) wersję próbną.

| Zasób | Limit domyślny |
| --- | --- |
| Okres próbny | 30 dni (po wygaśnięciu może zażądać nowej wersji próbnej) <br> Po wygaśnięciu informacje przechowywane są usuwane. |
| Maksymalna liczba kontenerów na subskrypcję (SQL, Gremlin, interfejs API tabel) | 1 |
| Maksymalna liczba kontenerów na subskrypcję (interfejs API MongoDB) | 3 |
| Maksymalna przepływność na kontener | 5000 |
| Maksymalna przepływność na wspólną bazę danych przepływności | 20000 |
| Maksymalna liczba magazynów na konto | 10 GB |

Wypróbuj Cosmos DB obsługuje dystrybucję globalną tylko w regionach Środkowe stany USA, Europa Północna i Azja Południowo-Wschodnia. Nie można utworzyć biletów pomocy technicznej systemu Azure dla usług try Azure Cosmos DB. Jednak pomoc techniczna jest świadczona dla subskrybentów z istniejącymi planami pomocy technicznej.

## <a name="azure-cosmos-db-free-tier-account-limits"></a>Azure Cosmos DB limity kont warstwy Bezpłatna

W poniższej tabeli wymieniono limity [Azure Cosmos DB kont warstwy bezpłatnej.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Zasób | Limit domyślny |
| --- | --- |
| Liczba kont warstwy Bezpłatna na subskrypcję platformy Azure | 1 |
| Czas trwania rabatu w warstwie Bezpłatna | Okres istnienia konta. Należy się zadecydować podczas tworzenia konta. |
| Maksymalna liczba jednostek RU na sekundę | 400 RU/s |
| Maksymalna ilość miejsca do magazynowania | 5 GB |
| Maksymalna liczba udostępnionych baz danych przepływności | 5 |
| Maksymalna liczba kontenerów w udostępnionej bazie danych przepływności | 25 <br>W przypadku kont warstwy Bezpłatna minimalna wartość RU/s dla udostępnionej bazy danych przepływności z maksymalnie 25 kontenerami wynosi 400 RU/s. |

Oprócz powyższych [wartości limity dla konta](#per-account-limits) są stosowane również do kont warstwy Bezpłatna.

> [!NOTE]
> Azure Cosmos DB bezpłatna jest różna od bezpłatnego konta platformy Azure. Bezpłatne konto platformy Azure oferuje środki na korzystanie z platformy Azure bezpłatnie, przez ograniczony czas. W przypadku korzystania z Azure Cosmos DB w ramach tego bezpłatnego konta uzyskasz 25 GB miejsca do magazynowania i 400 RU/s zainicjowanej przepływności przez 12 miesięcy.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [ogólnej dystrybucji](distribute-data-globally.md) i [partycjonowania](partitioning-overview.md) podstawowych pojęć dotyczących [Cosmos DB.](request-units.md)

Rozpocznij pracę z usługą Azure Cosmos DB, korzystając z jednego z naszych przewodników:

* [Rozpoczynanie pracy z interfejsem SQL usługi Azure Cosmos DB](create-sql-api-dotnet.md)
* [Rozpoczynanie pracy z interfejsem MongoDB usługi Azure Cosmos DB](create-mongodb-nodejs.md)
* [Rozpoczynanie pracy z interfejsem API rozwiązania Cassandra usługi Azure Cosmos DB](create-cassandra-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API programu Gremlin usługi Azure Cosmos DB](create-graph-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API tabeli usługi Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Wypróbuj usługę Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/)
