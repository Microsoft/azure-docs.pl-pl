---
title: Metryki zapytań SQL dotyczące Azure Cosmos DB interfejsu API SQL
description: Dowiedz się, jak instrumentować i debugować wydajność zapytań SQL Azure Cosmos DB żądania.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/23/2019
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: fedcdd55a465f5c09c331a0fa917811c349b15b1
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097230"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Tuning query performance with Azure Cosmos DB (Dostosowywanie wydajności zapytań w usłudze Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB udostępnia [interfejs API SQL do wykonywania zapytań dotyczących danych](./sql-query-getting-started.md), bez konieczności stosowania indeksów schematu lub pomocniczego. Ten artykuł zawiera następujące informacje dla deweloperów:

* Szczegółowe informacje o tym, jak działa wykonywanie zapytań SQL Azure Cosmos DB
* Szczegóły dotyczące żądania zapytania i nagłówków odpowiedzi oraz opcji zestawu SDK klienta
* Wskazówki i najlepsze rozwiązania dotyczące wydajności zapytań
* Przykłady użycia statystyk wykonywania SQL do debugowania wydajności zapytań

## <a name="about-sql-query-execution"></a>Informacje o wykonywaniu zapytań SQL

W Azure Cosmos DB dane są przechowywane w kontenerach, które mogą rosnąć do dowolnego [rozmiaru magazynu lub przepływności żądania](partitioning-overview.md). Azure Cosmos DB bezproblemowo skalować dane między partycjami fizycznymi w ramach okładek, aby obsłużyć wzrost ilości danych lub zwiększyć przepływność. Zapytania SQL można wydać do dowolnego kontenera przy użyciu interfejsu API REST lub jednego z obsługiwanych [zestawów SDK SQL](sql-api-sdk-dotnet.md).

Krótkie omówienie partycjonowania: definiujesz klucz partycji, taki jak "miasto", który określa sposób podziału danych między partycjami fizycznymi. Dane należące do jednego klucza partycji (na przykład "miasto" = = "Seattle") są przechowywane w partycji fizycznej, ale zazwyczaj pojedynczej partycji fizycznej ma wiele kluczy partycji. Gdy partycja osiągnie rozmiar magazynu, usługa bezproblemowo dzieli partycję na dwie nowe partycje i dzieli klucz partycji równomiernie między te partycje. Ponieważ partycje są przejściowe, interfejsy API używają abstrakcji zakresu kluczy partycji, który oznacza zakresy skrótów kluczy partycji. 

Po wydaniu zapytania do Azure Cosmos DB, zestaw SDK wykonuje następujące czynności logiczne:

* Przeanalizuj zapytanie SQL, aby określić plan wykonywania zapytania. 
* Jeśli zapytanie zawiera filtr względem klucza partycji, `SELECT * FROM c WHERE c.city = "Seattle"` na przykład, jest kierowany do pojedynczej partycji. Jeśli zapytanie nie ma filtru dla klucza partycji, to jest wykonywane we wszystkich partycjach, a wyniki są scalone po stronie klienta.
* Zapytanie jest wykonywane w ramach każdej partycji lub równolegle, na podstawie konfiguracji klienta. W ramach każdej partycji zapytanie może wykonać jedną lub kilka rund w zależności od złożoności zapytania, skonfigurowanego rozmiaru strony i przepływności dla kolekcji. Każde wykonanie zwraca liczbę [jednostek żądań](request-units.md) używanych przez wykonanie zapytania i opcjonalnie, statystyk wykonywania zapytań. 
* Zestaw SDK wykonuje podsumowanie wyników zapytania między partycjami. Na przykład, jeśli zapytanie zawiera zamówienie według między partycjami, wyniki z poszczególnych partycji są scalane — posortowane w celu zwrócenia wyników w kolejności sortowania globalnie. Jeśli zapytanie jest agregacją, na `COUNT` przykład, liczby z poszczególnych partycji są sumowane w celu uzyskania ogólnej liczby.

Zestawy SDK udostępniają różne opcje wykonywania zapytań. Na przykład w programie .NET te opcje są dostępne w `FeedOptions` klasie. W poniższej tabeli opisano te opcje i ich wpływ na czas wykonywania zapytania. 

| Opcja | Opis |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Musi być ustawiona na wartość true dla każdego zapytania, które wymaga wykonania w więcej niż jednej partycji. Jest to jawna flaga umożliwiająca świadome kompromisy w zakresie wydajności w czasie projektowania. |
| `EnableScanInQuery` | Musi być ustawiona na wartość true, jeśli wybrałeś opcję indeksowania, ale mimo to chcesz uruchomić zapytanie za pośrednictwem skanowania. Dotyczy tylko sytuacji, gdy indeksowanie dla żądanej ścieżki filtru jest wyłączone. | 
| `MaxItemCount` | Maksymalna liczba elementów do zwrócenia na serwer. Ustawienie wartości-1 umożliwia serwerowi zarządzanie liczbą elementów. Można też obniżyć tę wartość, aby pobrać tylko niewielką liczbę elementów na rejs rundy. 
| `MaxBufferedItemCount` | Jest to opcja po stronie klienta i służy do ograniczania zużycia pamięci podczas wykonywania zamówienia między partycjami przez program. Wyższa wartość ułatwia skrócenie opóźnienia sortowania między partycjami. |
| `MaxDegreeOfParallelism` | Pobiera lub ustawia liczbę równoczesnych uruchomień operacji po stronie klienta podczas równoległego wykonywania zapytań w usłudze Azure Cosmos Database. Wartość właściwości dodatniej ogranicza liczbę operacji współbieżnych do wartości zestawu. W przypadku ustawienia wartości mniejszej niż 0 system automatycznie decyduje o liczbie współbieżnych operacji do uruchomienia. |
| `PopulateQueryMetrics` | Umożliwia szczegółowe rejestrowanie statystyk czasu spędzonego w różnych fazach wykonywania zapytań, takich jak czas kompilacji, czas pętli indeksu i czas ładowania dokumentu. Aby zdiagnozować problemy z wydajnością zapytań, można udostępnić dane wyjściowe z statystyk zapytań w ramach pomocy technicznej platformy Azure. |
| `RequestContinuation` | Można wznowić wykonywanie zapytania, przekazując nieprzezroczysty token kontynuacji zwrócony przez dowolne zapytanie. Token kontynuacji hermetyzuje wszystkie Stany wymagane do wykonania zapytania. |
| `ResponseContinuationTokenLimitInKb` | Można ograniczyć maksymalny rozmiar tokenu kontynuacji zwracanego przez serwer. Może być konieczne ustawienie tego ustawienia, Jeśli host aplikacji ma limity rozmiaru nagłówka odpowiedzi. Ustawienie to może zwiększyć całkowity czas trwania i jednostek ru zużyty dla zapytania.  |

Załóżmy na przykład, że zażądano przykładowego zapytania dotyczącego klucza partycji w kolekcji z `/city` kluczem partycji i z obsługą jednostki przepływności 100 000 ru/s. Zażądano tego zapytania przy użyciu programu `CreateDocumentQuery<T>` .NET, takiego jak następujące:

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

Przedstawiony powyżej fragment kodu zestawu SDK odpowiada następującemu żądaniu interfejsu API REST:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

Każda Strona wykonywania zapytania odpowiada interfejsowi API REST `POST` z `Accept: application/query+json` nagłówkiem i kwerendzie SQL w treści. Każde zapytanie wykonuje co najmniej jedną rundę na serwerze z tokenem, który został przez siebie uruchomiony na `x-ms-continuation` serwerze, aby wznowić wykonywanie. Opcje konfiguracji w FeedOptions są przesyłane do serwera w postaci nagłówków żądania. Na przykład `MaxItemCount` odpowiada `x-ms-max-item-count` . 

Żądanie zwróci następujący (obcięty dla czytelności) odpowiedzi:

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

Następujące nagłówki odpowiedzi są zwracane z zapytania:

| Opcja | Opis |
| ------ | ----------- |
| `x-ms-item-count` | Liczba elementów zwróconych w odpowiedzi. Jest to zależne od podanej `x-ms-max-item-count` liczby elementów, które mogą być dopasowane do maksymalnego rozmiaru ładunku odpowiedzi, alokowanej przepływności i czasu wykonywania zapytania. |  
| `x-ms-continuation:` | Token kontynuacji, aby wznowić wykonywanie zapytania, jeśli są dostępne dodatkowe wyniki. | 
| `x-ms-documentdb-query-metrics` | Statystyka zapytania dla wykonania. Jest to rozdzielany ciąg zawierający dane statystyczne czasu spędzonego w różnych fazach wykonywania zapytania. Zwraca `x-ms-documentdb-populatequerymetrics` wartość, jeśli jest ustawiona na `True` . | 
| `x-ms-request-charge` | Liczba [jednostek żądań](request-units.md) zużytych przez zapytanie. | 

Aby uzyskać szczegółowe informacje na temat nagłówków i opcji żądań interfejsu API REST, zobacz [wykonywanie zapytań dotyczących zasobów przy użyciu interfejsu API REST](/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Najlepsze rozwiązania dotyczące wydajności zapytań
Poniżej przedstawiono najbardziej typowe czynniki wpływające na wydajność zapytań Azure Cosmos DB. Dig się do każdego z tych tematów w tym artykule.

| Czynnik | Porada | 
| ------ | -----| 
| Aprowizowana przepływność | Zmierz wartość RU na zapytanie i upewnij się, że masz wymaganą przepustowość zainicjowaną dla zapytań. | 
| Partycjonowanie i klucze partycji | Preferuj zapytania z wartością klucza partycji w klauzuli filtru w przypadku małych opóźnień. |
| Opcje zestawu SDK i zapytania | Postępuj zgodnie z najlepszymi rozwiązaniami dotyczącymi zestawu SDK, takimi jak łączność bezpośrednia, i dostrojenie opcji wykonywania zapytań |
| Opóźnienie sieci | Konto do obsługi obciążeń sieci w miarę i Użyj interfejsów API multihostingu, aby odczytać z najbliższego regionu. |
| Zasady indeksowania | Upewnij się, że masz wymagane ścieżki indeksowania/zasady dla zapytania. |
| Metryki wykonywania zapytania | Analizuj metryki wykonywania zapytania, aby identyfikować potencjalne wielokrotne zapisywanie kształtów zapytań i danych.  |

### <a name="provisioned-throughput"></a>Aprowizowana przepływność
W Cosmos DB można tworzyć kontenery danych, z których każdy ma zarezerwowaną przepływność wyrażoną w jednostkach żądania (RU) na sekundę. Odczytanie dokumentu o rozmiarze 1 KB to 1 RU, a każda operacja (łącznie z zapytaniami) jest znormalizowana do stałej liczby jednostek ru na podstawie jego złożoności. Na przykład jeśli masz 1000 RU/s zainicjowany dla kontenera i masz zapytanie, `SELECT * FROM c WHERE c.city = 'Seattle'` które wykorzystuje 5 jednostek ru, wówczas można wykonać (1000 ru/s)/(5 ru/zapytanie) = 200 zapytania/s takie zapytania na sekundę. 

Jeśli przesyłanych jest więcej niż 200 zapytań/s, usługa zaczyna ograniczać liczbę żądań przychodzących powyżej 200/s. Zestawy SDK automatycznie obsługują ten przypadek, wykonując wycofywania/ponów próbę, w związku z czym może być zauważalne wyższe opóźnienie dla tych zapytań. Zwiększenie zainicjowanej przepływności do wymaganej wartości zwiększa opóźnienia zapytań i przepływność. 

Aby dowiedzieć się więcej na temat jednostek żądania, zobacz [jednostki żądań](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Partycjonowanie i klucze partycji
W usłudze Azure Cosmos DB zapytania są przeważnie wykonywane w kolejności od najszybszego/nabardziej efektywnego do wolniejszego/mniej efektywnego. 

* Pobieranie na pojedynczym kluczu partycji i kluczu elementu
* Zapytanie z klauzulą filtru na pojedynczym kluczu partycji
* Kwerenda bez klauzuli filtru równości lub zakresu dla żadnej właściwości
* Zapytanie bez filtrów

Zapytania wymagające zapoznania się ze wszystkimi partycjami wymagają większego opóźnienia i mogą zużywać wyższe jednostek ru. Ponieważ każda partycja ma Automatyczne indeksowanie wszystkich właściwości, zapytanie może być obsługiwane efektywnie z indeksu w tym przypadku. Można wykonywać zapytania, które dzielą partycje szybciej, przy użyciu opcji równoległości.

Aby dowiedzieć się więcej na temat partycjonowania i kluczy partycji, zobacz [partycjonowanie w Azure Cosmos DB](partitioning-overview.md).

### <a name="sdk-and-query-options"></a>Opcje zestawu SDK i zapytania
Zobacz [porady dotyczące wydajności](performance-tips.md) i [testy wydajnościowe](performance-testing.md) , aby uzyskać najlepszą wydajność po stronie klienta z Azure Cosmos DB. Obejmuje to korzystanie z najnowszych zestawów SDK, Konfigurowanie konfiguracji specyficznych dla platformy, takich jak domyślna liczba połączeń, częstotliwość wyrzucania elementów bezużytecznych i używanie uproszczonych opcji łączności, takich jak Direct/TCP. 


#### <a name="max-item-count"></a>Maksymalna liczba elementów
W przypadku zapytań wartość `MaxItemCount` może mieć znaczny wpływ na pełny czas zapytania. Każda runda na serwerze zwróci wartość nie więcej niż liczbę elementów w `MaxItemCount` (domyślnie 100). Ustawienie tej opcji na wyższą wartość (-1 jest maksymalne i zalecane) poprawi czas trwania zapytania, ograniczając liczbę rund między serwerem a klientem, szczególnie w przypadku zapytań z dużymi zestawami wyników.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Maksymalny stopień równoległości
W przypadku zapytań Dostosuj program, `MaxDegreeOfParallelism` Aby zidentyfikować najlepsze konfiguracje dla aplikacji, szczególnie w przypadku wykonywania zapytań między partycjami (bez filtrowania wartości klucza partycji). `MaxDegreeOfParallelism`  kontroluje maksymalną liczbę równoległych zadań, czyli maksymalną liczbę partycji, które mają być odwiedzane równolegle. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

Załóżmy, że
* D = domyślna maksymalna liczba równoległych zadań (= łączna liczba procesorów na komputerze klienckim)
* P = Maksymalna liczba zadań równoległych określona przez użytkownika
* N = liczba partycji, które muszą być odwiedzane w celu odpowiedzi na zapytanie

Poniżej przedstawiono konsekwencje, w jaki sposób zapytania równoległe byłyby zachowane dla różnych wartości P.
* (P = = 0) => tryb serial
* (P = = 1) => maksimum jednego zadania
* (P > 1) => min (P, N) zadania równoległe 
* (P < 1) = zadania równoległe> min (N, D)

Informacje o wersji zestawu SDK i szczegóły dotyczące zaimplementowanych klas i metod można znaleźć w temacie [zestawy SDK SQL](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Opóźnienie sieci
Zobacz [Azure Cosmos DB globalnej dystrybucji](tutorial-global-distribution-sql-api.md) , jak skonfigurować dystrybucję globalną i połączyć się z najbliższym regionem. Opóźnienie sieci ma znaczny wpływ na wydajność zapytań, gdy konieczne jest wykonanie wielu operacji rundy lub pobranie dużego zestawu wyników zapytania. 

W sekcji metryki wykonywania zapytania wyjaśniono, jak pobrać czas wykonywania zapytań ( `totalExecutionTimeInMs` ), aby można było odróżnić czas trwania wykonywania zapytania i czas spędzony na przeniesieniu do sieci.

### <a name="indexing-policy"></a>Zasady indeksowania
Zobacz [Konfigurowanie zasad indeksowania](index-policy.md) dla ścieżek indeksowania, rodzajów i trybów oraz ich wpływu na wykonywanie zapytań. Domyślnie zasady indeksowania wykorzystują indeksowanie skrótów dla ciągów, które są skuteczne dla zapytań równości, ale nie dla zapytań zakresowych/kolejności według zapytań. Jeśli potrzebujesz zapytań zakresowych dla ciągów, zalecamy określenie typu indeksu zakresu dla wszystkich ciągów. 

Domyślnie Azure Cosmos DB będzie stosować Automatyczne indeksowanie do wszystkich danych. W przypadku scenariuszy wstawiania o wysokiej wydajności należy rozważyć wykluczenie ścieżek, ponieważ spowoduje to zmniejszenie kosztu RU dla każdej operacji wstawiania. 

## <a name="query-execution-metrics"></a>Metryki wykonywania zapytania
Możesz uzyskać szczegółowe metryki wykonywania zapytania, przekazując opcjonalne `x-ms-documentdb-populatequerymetrics` nagłówki ( `FeedOptions.PopulateQueryMetrics` w zestawie SDK .NET). Wartość zwrócona w `x-ms-documentdb-query-metrics` ma następujące pary klucz-wartość przeznaczone do zaawansowanego rozwiązywania problemów dotyczących wykonywania zapytania. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| Metryka | Jednostka | Opis | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | milisekundy | Czas wykonywania zapytania | 
| `queryCompileTimeInMs` | milisekundy | Czas kompilowania zapytania  | 
| `queryLogicalPlanBuildTimeInMs` | milisekundy | Czas na utworzenie logicznego planu zapytania | 
| `queryPhysicalPlanBuildTimeInMs` | milisekundy | Czas na utworzenie planu zapytania fizycznego | 
| `queryOptimizationTimeInMs` | milisekundy | Czas poświęcony na optymalizację zapytania | 
| `VMExecutionTimeInMs` | milisekundy | Czas spędzony w czasie wykonywania zapytania | 
| `indexLookupTimeInMs` | milisekundy | Czas spędzony w warstwie indeksu fizycznego | 
| `documentLoadTimeInMs` | milisekundy | Czas poświęcony na ładowanie dokumentów  | 
| `systemFunctionExecuteTimeInMs` | milisekundy | Łączny czas wykonywania funkcji systemu (wbudowane) w milisekundach  | 
| `userFunctionExecuteTimeInMs` | milisekundy | Łączny czas wykonywania funkcji zdefiniowanych przez użytkownika w milisekundach | 
| `retrievedDocumentCount` | count | Łączna liczba pobranych dokumentów  | 
| `retrievedDocumentSize` | szybkość | Łączny rozmiar pobranych dokumentów w bajtach  | 
| `outputDocumentCount` | count | Liczba dokumentów wyjściowych | 
| `writeOutputTimeInMs` | milisekundy | Czas wykonywania zapytania w milisekundach | 
| `indexUtilizationRatio` | współczynnik (<= 1) | Stosunek liczby dokumentów dopasowanych przez filtr do liczby załadowanych dokumentów  | 

Zestawy SDK klienta mogą wewnętrznie wykonywać wiele operacji zapytania w celu obsługi zapytania w ramach każdej partycji. Klient wykonuje więcej niż jedno wywołanie na partycję, jeśli łączna liczba wyników przekroczy `x-ms-max-item-count` , jeśli zapytanie przekracza zainicjowaną przepływność dla partycji lub jeśli ładunek zapytania osiągnie maksymalny rozmiar na stronę lub jeśli zapytanie osiągnie limit czasu przydzielonego systemu. Każde wykonanie zapytania częściowego zwraca `x-ms-documentdb-query-metrics` dla tej strony. 

Oto kilka przykładowych zapytań i sposób interpretowania niektórych metryk zwracanych z wykonywania zapytania: 

| Zapytanie | Przykładowa Metryka | Opis | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Liczba pobranych dokumentów wynosi 100 + 1, aby pasowała do klauzuli TOP. Czas zapytania jest głównie poświęcany na `WriteOutputTime` i `DocumentLoadTime` ponieważ jest to skanowanie. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount jest teraz wyższa (500 + 1, aby dopasować klauzulę TOP). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Informacje o 0,9 MS odbywają się w IndexLookupTime na potrzeby wyszukiwania kluczy, ponieważ jest to wyszukiwanie w indeksie `/N/?` . | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Nieco więcej czasu (1,7 ms) spędzony w IndexLookupTime przez skanowanie zakresu, ponieważ jest to wyszukiwanie w indeksie `/N/?` . | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Ten sam czas poświęcany na `DocumentLoadTime` poprzednie zapytania, ale `WriteOutputTime` jest niższy, ponieważ obsługujemy tylko jedną właściwość. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Informacje o 213 MS poświęcają na `UserDefinedFunctionExecutionTime` wykonywanie operacji UDF dla każdej wartości `c.N` . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Informacje o 0,6 MS odbywają się w `IndexLookupTime` dniu `/Name/?` . Większość czasu wykonywania zapytania (~ 7 ms) w `SystemFunctionExecutionTime` . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Zapytanie jest wykonywane w ramach skanowania, ponieważ jest w nim stosowane `LOWER` , a 500 z 2491 pobrane dokumenty są zwracane. |


## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej na temat obsługiwanych operatorów zapytań SQL i słów kluczowych, zobacz [zapytanie SQL](sql-query-getting-started.md). 
* Aby dowiedzieć się więcej o jednostkach żądania, zobacz [jednostki żądań](request-units.md).
* Aby dowiedzieć się więcej na temat zasad indeksowania, zobacz [zasady indeksowania](index-policy.md)