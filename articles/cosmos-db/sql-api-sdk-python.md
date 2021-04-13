---
title: Azure Cosmos DB zasobów interfejsu API języka Python, zestawu SDK & SQL
description: Dowiedz się wszystkiego o interfejsie API i zestawie SDK języka Python SQL, w tym o datach wydaniach, datach wycofania i zmianach wprowadzonych między poszczególnymi wersjami zestawu SDK Azure Cosmos DB Python.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 04/06/2021
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: ff551c5d677029f39d9a3db3a64f3d03e2c57eba
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366036"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Zestaw Python SDK usługi Azure Cosmos DB na potrzeby interfejsu API SQL: Informacje o wersji i zasoby
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Zestaw SDK .NET w wersji 3](sql-api-sdk-dotnet-standard.md)
> * [Zestaw .NET SDK w wersji 2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK 2](sql-api-sdk-dotnet-core.md)
> * [Zestaw SDK zestawienia zmian platformy .NET w wersji 2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK 4](sql-api-sdk-java-v4.md)
> * [Async Java SDK 2](sql-api-sdk-async-java.md)
> * [Sync Java SDK 2](sql-api-sdk-java.md)
> * [Spring Data 2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data 3](sql-api-sdk-java-spring-v3.md)
> * [Łącznik OLTP platformy Spark 3](sql-api-sdk-java-spark-v3.md)
> * [Łącznik OLTP platformy Spark 2](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Wykonywanie zbiorcze — .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonywania zbiorczego — Java](sql-api-sdk-bulk-executor-java.md)

| Strona| Link |
|---|---|
|**Pobierz zestaw SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Dokumentacja interfejsu API**|[Dokumentacja referencyjna interfejsu API języka Python](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos?view=azure-python&preserve-view=true)|
|**Instrukcje instalacji zestawu SDK**|[Instrukcje instalacji zestawu SDK dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Wprowadzenie**|[Wprowadzenie do zestawu SDK języka Python](create-sql-api-python.md)|
|**Bieżąca obsługiwana platforma**|[Python 2.7](https://www.python.org/downloads/) i [Python 3.6+](https://www.python.org/downloads/)|

## <a name="release-history"></a>Historia wersji

## <a name="420"></a>4.2.0

**Poprawki**
- Usunięto usterkę, która powoduje, że token kontynuacji nie jest honorowany, query_iterable jest używany do uzyskania wyników według strony.
- Usunięto usterkę, która powoduje, że tokeny zasobów nie są honorowane dla odczytów i usuwania dokumentów. 

**Nowe funkcje**
- Dodano obsługę przekazywania `partitionKey` podczas wykonywania zapytań w zestawieniach zmian.

## <a name="410"></a>4.1.0

- Dodano ostrzeżenie o zatrzymaniu trybu indeksowania "z opóźnieniem". Zaplecza nie umożliwiają już tworzenia kontenerów w tym trybie i zamiast tego ustawią je na spójne.

**Nowe funkcje**
- Dodano możliwość ustawienia analitycznego TTL magazynu podczas tworzenia nowego kontenera.

**Poprawki**
- Poprawiono obsługę `dicts` jako danych wejściowych dla get_client API.
- Naprawiono zgodność języka Python 2/3 w iteratorach zapytań.
- Naprawiono błąd wskazówki dotyczącej typu.
- Usunięto usterkę, która pozwalała na to, że nagłówki opcji nie były dodawane upsert_item funkcji. 
- Naprawiono błąd, który był wyświetlany, gdy w elementie był używany identyfikator niebędący ciągiem. Teraz zgłasza wartość TypeError, a nie AttributeError.


## <a name="400"></a>4.0.0

* Stabilna wersja.
* Dodano do potoku wartość HttpLoggingPolicy, aby umożliwić przekazywanie niestandardowego rejestratora nagłówków żądań i odpowiedzi.

### <a name="400b6"></a>4.0.0b6

* Usunięto usterkę w synchronized_request dla interfejsów API multimediów.
* Usunięto tryby MediaReadMode i MediaRequestTimeout z connectionPolicy, ponieważ żądania multimediów nie są obsługiwane.

### <a name="400b5"></a>4.0.0b5

* Moduł azure.cosmos.errors został wycofany i zastąpiony przez azure.cosmos.exceptions
* Parametry warunku dostępu ( , , ) zostały wycofane na `access_condition` `if_match` rzecz `if_none_match` oddzielnych parametrów i `match_condition` `etag` .
* Usunięto usterkę w dostawcy mapowania routingu.
* Dodano obsługę zapytań Distinct, Offset i Limit.
* Domyślny kontekst wykonywania zapytania dokumentu jest teraz używany dla

  * Zapytania dotyczące zestawienia zmian
  * zapytania z pojedynczą partycją ( `partitionkey` , jest dostępne w `partitionKeyRangeId` opcjach)
  * Zapytania nieudokumentowe

* Błędy agregacji na wielu partycjach z włączoną zapytaniem między partycjami ustawioną na wartość true, ale nie ma słowa kluczowego "value"
* Trafia punkt końcowy planu zapytania dla innych scenariuszy w celu pobrania planu zapytania
* Dodano `__repr__` obsługę obiektów jednostek usługi Cosmos.
* Zaktualizowano dokumentację.

### <a name="400b4"></a>4.0.0b4

* Dodano obsługę argumentu słowa kluczowego do wszystkich operacji w celu określenia bezwzględnego limitu czasu w sekundach, w którym `timeout` należy ukończyć operację. Jeśli wartość limitu czasu zostanie przekroczona, zostanie `azure.cosmos.errors.CosmosClientTimeoutError` podniesiona wartość .

* Dodano nową `ConnectionRetryPolicy` w celu zarządzania zachowaniem ponawiania podczas błędów połączenia HTTP.

* Dodano nowy konstruktor i argumenty słowa kluczowego konfiguracji dla operacji:

  * `retry_total` — Maksymalna liczba ponownych prób.
  * `retry_backoff_max` — Maksymalny czas oczekiwania na ponowną próbę w sekundach.
  * `retry_fixed_interval` - Naprawiono interwał ponawiania prób w milisekundach.
  * `retry_read` — Maksymalna liczba gniazd podczas ponawiania prób odczytu.
  * `retry_connect` - Maksymalna liczba ponownych prób błędu połączenia.
  * `retry_status` — Maksymalna liczba ponownych prób w przypadku kodów stanu błędu.
  * `retry_on_status_codes` — lista określonych kodów stanu do ponowień.
  * `retry_backoff_factor` - Współczynnik obliczania czasu oczekiwania między ponowieniami prób.

### <a name="400b3"></a>4.0.0b3

* Dodano `create_database_if_not_exists()` funkcje i odpowiednio do obiektów `create_container_if_not_exists` CosmosClient i Database.

### <a name="400b2"></a>4.0.0b2

* Wersja 4.0.0b2 to druga iteracja w naszych wysiłkach w celu skompilowania biblioteki klienta, która odpowiada najlepszym praktykom w zakresie języka Python.

**Fundamentalne zmiany**

* Połączenie klienta zostało dostosowane w celu obsługi potoku HTTP zdefiniowanego `azure.core.pipeline` w .

* Nazwy obiektów interakcyjnych zostały zmienione na proxy. Obejmuje to następujące działania:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* Konstruktor klasy `CosmosClient` został zaktualizowany:

  * Nazwa `auth` parametru została zmieniona na i teraz będzie bezpośrednio przyjmować `credential` typ uwierzytelniania. Oznacza to, że można przejść do wartości klucza podstawowego, słownika tokenów zasobów lub listy uprawnień. Jednak stary format słownika jest nadal obsługiwany.

  * Parametr został wykonany jako parametr tylko słowo kluczowe i chociaż jest nadal obsługiwany, każdy z poszczególnych atrybutów zasad może być teraz przekazywany jako jawne argumenty `connection_policy` ze słowem kluczowym:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* Dodano nowy konstruktor w celu umożliwienia tworzenia za pośrednictwem `CosmosClient` parametrów połączenia pobranych z Azure Portal.

* Niektóre `read_all` operacje zostały zmienione na `list` operacje:

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Wszystkie operacje, które `request_options` przyjmą parametry lub , zostały przeniesione do `feed_options` parametrów tylko słowa kluczowego. Ponadto, mimo że te słowniki opcji są nadal obsługiwane, każda z poszczególnych opcji w słowniku jest teraz obsługiwana jako jawne argumenty ze słowem kluczowym.

* Hierarchia błędów jest teraz dziedziczona `azure.core.AzureError` z :

  * Zmieniono nazwę polecenia `HTTPFailure` na `CosmosHttpResponseError`
  * `JSONParseFailure` został usunięty i zastąpiony przez `azure.core.DecodeError`
  * Dodano dodatkowe błędy dla określonych kodów odpowiedzi:
    * `CosmosResourceNotFoundError` dla stanu 404
    * `CosmosResourceExistsError` dla stanu 409
    * `CosmosAccessConditionFailedError` dla stanu 412

* `CosmosClient` Można teraz uruchomić w menedżerze kontekstu, aby obsłużyć zamykanie połączenia klienta.

* Odpowiedzi iterowalne (na przykład odpowiedzi na zapytania i odpowiedzi listy) są teraz typu `azure.core.paging.ItemPaged` . Metoda `fetch_next_block` została zastąpiona przez iterator pomocniczy, do którego uzyskuje dostęp `by_page` metoda .

### <a name="400b1"></a>4.0.0b1

Wersja 4.0.0b1 to pierwsza wersja zapoznawcza naszych wysiłków w celu utworzenia przyjaznej dla użytkownika biblioteki klienta, która odpowiada najlepszym praktykom w zakresie języka Python. Aby uzyskać więcej informacji na ten temat oraz wersje zapoznawcze innych bibliotek zestawu Azure SDK, odwiedź stronę https://aka.ms/azure-sdk-preview1-python .

**Istotne zmiany: Nowy projekt interfejsu API**

* Zakres operacji jest teraz ograniczony do określonego klienta:

  * `CosmosClient`: ten klient obsługuje operacje na poziomie konta. Obejmuje to zarządzanie właściwościami usługi i wyświetlanie listy baz danych w ramach konta.
  * `Database`: ten klient obsługuje operacje na poziomie bazy danych. Obejmuje to tworzenie i usuwanie kontenerów, użytkowników i procedur składowanych. Dostęp do niego można uzyskać z wystąpienia klasy cosmosClient według nazwy.
  * `Container`: ten klient obsługuje operacje dla określonego kontenera. Obejmuje to wykonywanie zapytań i wstawianie elementów oraz zarządzanie właściwościami.
  * `User`: ten klient obsługuje operacje dla określonego użytkownika. Obejmuje to dodawanie i usuwanie uprawnień oraz zarządzanie właściwościami użytkownika.

    Dostęp do tych klientów można uzyskać, przechodząc w dół hierarchii klienta przy użyciu `get_<child>_client` metody . Aby uzyskać szczegółowe informacje na temat nowego interfejsu API, zobacz [dokumentację referencyjną](https://aka.ms/azsdk-python-cosmos-ref).

* Dostęp do klientów jest uzyskiwany za pomocą nazwy, a nie identyfikatora. Nie trzeba tworzyć linków w celu połączenia ciągów.

* Nie trzeba już importować typów i metod z poszczególnych modułów. Publiczny obszar powierzchni interfejsu API jest dostępny bezpośrednio w `azure.cosmos` pakiecie.

* Właściwości poszczególnych żądań można podano jako argumenty słów kluczowych zamiast konstruowania oddzielnego `RequestOptions` wystąpienia.

### <a name="302"></a>3.0.2

* Dodano obsługę typu danych MultiPolygon
* Poprawka usterki w zasadach ponawiania odczytu sesji
* Poprawka usterki dotyczącej nieprawidłowych problemów z uzupełnianiem podczas dekodowania ciągów base 64

### <a name="301"></a>3.0.1

* Poprawka usterki w u usługi LocationCache
* Logika ponawiania próby punktu końcowego naprawiana przez usterkę
* Poprawiono dokumentację

### <a name="300"></a>3.0.0

* Dodano obsługę zapisu w wielu regionach
* Zmiany nazw
  * DocumentClient do CosmosClient
  * Zbieranie do kontenera
  * Dokument do elementu
  * Zaktualizowano nazwę pakietu do "azure-cosmos"
  * Przestrzeń nazw zaktualizowana do "azure.cosmos"

### <a name="233"></a>2.3.3

* Dodano obsługę serwera proxy
* Dodano obsługę odczytywania zestawienia zmian
* Dodano obsługę nagłówków limitu przydziału kolekcji
* Poprawka usterki dla problemu z dużymi tokenami sesji
* Poprawka usterki dla interfejsu API ReadMedia
* Poprawka usterki w pamięci podręcznej zakresu kluczy partycji

### <a name="232"></a>2.3.2

* Dodano obsługę domyślnych ponownych prób w przypadku problemów z połączeniem.

### <a name="231"></a>2.3.1

* Zaktualizowano dokumentację w celu Azure Cosmos DB zamiast usługi Azure DocumentDB.

### <a name="230"></a>2.3.0

* Ta wersja zestawu SDK wymaga najnowszej wersji Azure Cosmos DB Emulator dostępnej do pobrania ze strony https://aka.ms/cosmosdb-emulator .

### <a name="221"></a>2.2.1

* Poprawka usterki dla zagregowanego dyktowania
* Poprawka usterki dla przycinania ukośników w linku zasobu
* testy kodowania Unicode

### <a name="220"></a>2.2.0

* Dodano obsługę funkcji Jednostki żądania na minutę (RU/m).
* Dodano obsługę nowego poziomu spójności o nazwie ConsistentPrefix.

### <a name="210"></a>2.1.0

* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i AVG).
* Dodano opcję wyłączania weryfikacji SSL podczas uruchamiania w emulatorze usługi DocumentDB.
* Usunięto moduł ograniczeń żądań zależnych, który ma mieć dokładnie 2.10.0.
* Obniżono minimalną przepływność w kolekcjach partycjonowanych z 10 100 RU/s do 2500 RU/s.
* Dodano obsługę włączania rejestrowania skryptów podczas wykonywania procedury składowanej.
* W tej wersji wersja interfejsu API REST przesunała się do wersji "2017-01-19".

### <a name="201"></a>2.0.1

* Dokonano zmian redakcyjnych komentarzy do dokumentacji.

### <a name="200"></a>2.0.0

* Dodano obsługę języka Python 3.5.
* Dodano obsługę puli połączeń przy użyciu modułu requests.
* Dodano obsługę spójności sesji.
* Dodano obsługę zapytań TOP/ORDERBY dla kolekcji partycjonowanych.

### <a name="190"></a>1.9.0

* Dodano obsługę zasad ponawiania dla żądań z ograniczeniami. (Żądania ograniczone otrzymują wyjątek o zbyt dużej szybkości żądań, kod błędu 429). Domyślnie baza danych DocumentDB ponawia próbę dziewięć razy dla każdego żądania, gdy zostanie napotkany kod błędu 429, honorując czas retryAfter w nagłówku odpowiedzi.
  Stały czas interwału ponawiania można teraz ustawić jako część właściwości RetryOptions obiektu ConnectionPolicy, jeśli chcesz zignorować czas retryAfter zwracany przez serwer między ponowieniami.
  Baza danych DocumentDB czeka teraz maksymalnie 30 sekund dla każdego żądania, które jest ograniczane (niezależnie od liczby ponownych prób) i zwraca odpowiedź z kodem błędu 429.
  Tym razem można również przesłonić właściwość RetryOptions obiektu ConnectionPolicy.

* Usługa DocumentDB zwraca teraz nagłówki odpowiedzi x-ms-throttle-retry-count i x-ms-throttle-retry-wait-time-ms jako nagłówki odpowiedzi w każdym żądaniu w celu określenia liczby ponownych prób ograniczenia i skumulowanego czasu oczekiwania żądania między ponowieniami.

* Usunięto klasę RetryPolicy i odpowiadającą jej właściwość (retry_policy) uwycznioną w klasie document_client, a zamiast tego wprowadzono klasę RetryOptions ujawniając właściwość RetryOptions klasy ConnectionPolicy, która może służyć do zastępowania niektórych domyślnych opcji ponawiania.

### <a name="180"></a>1.8.0

* Dodano obsługę kont bazy danych replikowanych geograficznie.
* Poprawki testów służące do przenoszenia globalnego hosta i klucza masterKey do poszczególnych klas testów.

### <a name="170"></a>1.7.0

* Dodano obsługę funkcji czasu wygaśnięcia (TTL) dla dokumentów.

### <a name="161"></a>1.6.1

* Poprawki błędów związane z partycjonowaniem po stronie serwera w celu zezwalania na znaki specjalne w ścieżce klucza partycji.

### <a name="160"></a>1.6.0

* Dodano obsługę funkcji kolekcji partycjonowanych po stronie serwera.

### <a name="150"></a>1.5.0

* Dodano platformę fragmentowania po stronie klienta do zestawu SDK. Zaimplementowano klasy HashPartionResolver i RangePartitionResolver.

### <a name="142"></a>1.4.2

* Zaim implementuj upsert. Dodano nowe metody UpsertXXX do obsługi funkcji Upsert.
* Zaim ID-Based routingu. Brak zmian publicznego interfejsu API, wszystkie zmiany wewnętrzne.

### <a name="130"></a>1.3.0

* Pominięto wydanie, aby numer wersji był wyrównany z innymi zestawami SDK

### <a name="120"></a>1.2.0

* Obsługuje indeks geoprzestrzenny.
* Weryfikuje właściwość ID dla wszystkich zasobów. Identyfikatory zasobów nie mogą zawierać `?, /, #, \\` znaków ani kończyć się spacją.
* Dodaje nowy nagłówek "postęp przekształcania indeksu" do klasy ResourceResponse.

### <a name="110"></a>1.1.0

* Implementuje zasady indeksowania w wersji 2

### <a name="101"></a>1.0.1

* Obsługuje połączenie serwera proxy

## <a name="release--retirement-dates"></a>Daty wycofania & wersji

Firma Microsoft udostępnia powiadomienie co najmniej **12 miesięcy** przed wycofaniem zestawu SDK, aby ułatwić przejście do nowszej/obsługiwanej wersji. Nowe funkcje i optymalizacje są dodawane tylko do bieżącego zestawu SDK, dlatego zaleca się, aby zawsze uaktualniać do najnowszej wersji zestawu SDK tak szybko, jak to możliwe.

> [!WARNING]
> Po 31 sierpnia 2022 r. usługa Azure Cosmos DB nie będzie już świadczyć poprawek błędów ani zapewniać obsługi wersji 1.x i 2.x zestawu AZURE COSMOS DB Python SDK dla interfejsu API SQL. Jeśli nie chcesz uaktualniać, żądania wysyłane z wersji 1.x i 2.x zestawu SDK będą nadal obsługiwane przez Azure Cosmos DB usługi.

| Wersja | Data wydania | Data wycofania |
| --- | --- | --- |
| [4.2.0](#420) |9 października 2020 r. |--- |
| [4.1.0](#410) |10 sierpnia 2020 r. |--- |
| [4.0.0](#400) |20 maja 2020 r. |--- |
| [3.0.2](#302) |15 listopada 2018 r. |--- |
| [3.0.1](#301) |4 października 2018 r. |--- |
| [2.3.3](#233) |8 września 2018 r. |31 sierpnia 2022 r. |
| [2.3.2](#232) |8 maja 2018 r. |31 sierpnia 2022 r. |
| [2.3.1](#231) |21 grudnia 2017 r. |31 sierpnia 2022 r. |
| [2.3.0](#230) |10 listopada 2017 r. |31 sierpnia 2022 r. |
| [2.2.1](#221) |29 września 2017 r. |31 sierpnia 2022 r. |
| [2.2.0](#220) |10 maja 2017 |31 sierpnia 2022 r. |
| [2.1.0](#210) |1 maja 2017 r. |31 sierpnia 2022 r. |
| [2.0.1](#201) |30 października 2016 r. |31 sierpnia 2022 r. |
| [2.0.0](#200) |29 września 2016 r. |31 sierpnia 2022 r. |
| [1.9.0](#190) |7 lipca 2016 r. |31 sierpnia 2022 r. |
| [1.8.0](#180) |14 czerwca 2016 r. |31 sierpnia 2022 r. |
| [1.7.0](#170) |26 kwietnia 2016 |31 sierpnia 2022 r. |
| [1.6.1](#161) |8 kwietnia 2016 r. |31 sierpnia 2022 r. |
| [1.6.0](#160) |29 marca 2016 r. |31 sierpnia 2022 r. |
| [1.5.0](#150) |3 stycznia 2016 r. |31 sierpnia 2022 r. |
| [1.4.2](#142) |6 października 2015 r. |31 sierpnia 2022 r. |
| 1.4.1 |6 października 2015 r. |31 sierpnia 2022 r. |
| [1.2.0](#120) |6 sierpnia 2015 r. |31 sierpnia 2022 r. |
| [1.1.0](#110) |9 lipca 2015 r. |31 sierpnia 2022 r. |
| [1.0.1](#101) |25 maja 2015 r. |31 sierpnia 2022 r. |
| 1.0.0 |7 kwietnia 2015 r. |31 sierpnia 2022 r. |
| 0.9.4 — prelease |14 stycznia 2015 r. |29 lutego 2016 r. |
| 0.9.3 — prelease |9 grudnia 2014 r. |29 lutego 2016 r. |
| 0.9.2 — prelease |25 listopada 2014 r. |29 lutego 2016 r. |
| 0.9.1 — wstępnie |23 września 2014 r. |29 lutego 2016 r. |
| 0.9.0 — wstępnie |21 sierpnia 2014 r. |29 lutego 2016 r. |

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
