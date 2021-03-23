---
title: Azure Cosmos DB interfejsu API języka SQL Python, zasoby & zestawu SDK
description: Dowiedz się więcej o interfejsie API języka SQL Python i zestawie SDK, w tym o datach wydania, datach wycofania i zmianach między poszczególnymi wersjami Azure Cosmos DB Python SDK.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 08/12/2020
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: 470ae497acab4c75e83a13e485d1bcb118485ab9
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799517"
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
> * [Łącznik platformy Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Wykonawca zbiorczy — .NET V2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonywania zbiorczego — Java](sql-api-sdk-bulk-executor-java.md)

| Strona| Link |
|---|---|
|**Pobierz zestaw SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API języka Python](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos?view=azure-python&preserve-view=true)|
|**Instrukcje dotyczące instalacji zestawu SDK**|[Instrukcje dotyczące instalacji zestawu SDK języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Wprowadzenie**|[Wprowadzenie do zestawu SDK języka Python](create-sql-api-python.md)|
|**Bieżąca obsługiwana platforma**|[Python 2,7](https://www.python.org/downloads/) i [Python 3.6 +](https://www.python.org/downloads/)|

## <a name="release-history"></a>Historia wersji

## <a name="420"></a>4.2.0

**Poprawki błędów**
- Naprawiono usterkę, w której token kontynuacji nie jest uznawany, gdy query_iterable jest używany do uzyskania wyników według strony.
- Rozwiązano problem polegający na tym, że tokeny zasobów nie są honorowane na potrzeby operacji odczytu i usuwania dokumentów. 

**Nowe funkcje**
- Dodano obsługę przekazywania `partitionKey` podczas wykonywania zapytania dotyczącego źródła zmian.

## <a name="410"></a>4.1.0

- Dodano ostrzeżenie o zaniechaniu trybu indeksowania "z opóźnieniem". Zaplecze nie zezwala już na tworzenie kontenerów w tym trybie i ustawi ich spójność.

**Nowe funkcje**
- Dodano możliwość ustawienia czasu wygaśnięcia magazynu analitycznego podczas tworzenia nowego kontenera.

**Poprawki błędów**
- Stała obsługa `dicts` jako dane wejściowe dla Get_client interfejsów API.
- Stała zgodność z językiem Python 2/3 w iteratorach zapytań.
- Błąd wskazówki dotyczącej ustalonego typu.
- Naprawiono usterkę, w której nagłówki opcji nie zostały dodane do funkcji upsert_item. 
- Naprawiono błąd wywoływany, gdy w elemencie jest używany identyfikator niebędący ciągiem. Teraz podnosi TypeError, a nie AttributeError.


## <a name="400"></a>4.0.0

* Stabilna wersja.
* Dodano HttpLoggingPolicy do potoku, aby umożliwić przekazywanie w niestandardowym rejestratorze dla nagłówków żądań i odpowiedzi.

### <a name="400b6"></a>4.0.0 B6

* Naprawiono usterkę w synchronized_request dla interfejsów API multimediów.
* Usunięte MediaReadMode i MediaRequestTimeout z ConnectionPolicy jako żądania multimediów nie są obsługiwane.

### <a name="400b5"></a>4.0.0 B5

* moduł Azure. Cosmos. Errors został uznany za przestarzały i zastąpiony przez platformę Azure. Cosmos. Exceptions
* Parametry warunku dostępu ( `access_condition` , `if_match` , `if_none_match` ) zostały zaniechane na rzecz oddzielnych `match_condition` i `etag` parametrów.
* Naprawiono usterkę w dostawcy mapy routingu.
* Dodano obsługę różnych zapytań, przesunięcia i limitu.
* Kontekst wykonywania zapytania dokumentu domyślnego został teraz użyty dla

  * Zapytania dotyczące zmiany kanałów informacyjnych
  * zapytania o pojedynczej partycji ( `partitionkey` , `partitionKeyRangeId` dostępne w opcjach)
  * Zapytania niebędące dokumentami

* Liczba błędów dla agregacji w wielu partycjach z opcją Włącz zapytanie między partycjami z ustawioną wartością prawda, ale nie ma słowa kluczowego "value"
* Punkt końcowy planu zapytania trafień dla innych scenariuszy pobierania planu zapytania
* Dodano `__repr__` obsługę obiektów jednostek Cosmos.
* Zaktualizowana dokumentacja.

### <a name="400b4"></a>4.0.0 B4

* Dodano obsługę `timeout` argumentu słowa kluczowego do wszystkich operacji, aby określić bezwzględny limit czasu (w sekundach), w którym operacja musi zostać zakończona. Jeśli zostanie przekroczona wartość limitu czasu, `azure.cosmos.errors.CosmosClientTimeoutError` zostanie wyniesiona.

* Dodano nowy element `ConnectionRetryPolicy` do zarządzania zachowaniem ponowień podczas błędów połączenia http.

* Dodano nowy Konstruktor i argumenty słowa kluczowego konfiguracji dla operacji:

  * `retry_total` -Maksymalna liczba ponownych prób.
  * `retry_backoff_max` — Maksymalny czas oczekiwania na ponowienie próby (w sekundach).
  * `retry_fixed_interval` -Stały interwał ponawiania prób w milisekundach.
  * `retry_read` — Maksymalna liczba ponownych prób odczytu gniazd.
  * `retry_connect` -Maksymalna liczba prób ponowienia błędu połączenia.
  * `retry_status` -Maksymalna liczba ponownych prób w kodach stanu błędu.
  * `retry_on_status_codes` -Lista określonych kodów stanu do ponowienia próby.
  * `retry_backoff_factor` -Factor oblicza czas oczekiwania między ponownymi próbami.

### <a name="400b3"></a>4.0.0 B3

* Dodano `create_database_if_not_exists()` `create_container_if_not_exists` odpowiednio i funkcje do CosmosClient i bazy danych.

### <a name="400b2"></a>4.0.0 B2

* Wersja 4.0.0 B2 to druga iteracja w naszych wysiłkach w celu skompilowania biblioteki klienckiej, która odpowiada najlepszym praktykom języka Python.

**Fundamentalne zmiany**

* Połączenie klienta zostało dostosowane do korzystania z potoku HTTP zdefiniowanego w `azure.core.pipeline` .

* Nazwy obiektów interaktywnych zostały teraz zmienione jako serwery proxy. Obejmuje to następujące działania:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* Konstruktor programu `CosmosClient` został zaktualizowany:

  * `auth`Nazwa parametru została zmieniona na `credential` i teraz będzie można bezpośrednio zastosować typ uwierzytelniania. Oznacza to, że wartość klucza podstawowego, słownik tokenów zasobów lub lista uprawnień może zostać przeniesiona. Jednak stary format słownika jest nadal obsługiwany.

  * `connection_policy`Parametr został wykonany jako parametr tylko dla słowa kluczowego, a mimo to jest obsługiwany, każdy z poszczególnych atrybutów zasad może teraz zostać przekazano jako argumenty jawnie zdefiniowanego słowa kluczowego:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* Dodano nowy Konstruktor do `CosmosClient` programu w celu umożliwienia tworzenia za pośrednictwem parametrów połączenia pobranych z Azure Portal.

* `read_all`Zmieniono nazwę niektórych operacji na `list` operacje:

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Wszystkie operacje przyjmujące `request_options` lub `feed_options` Parametry, które zostały przeniesione do parametrów samego słowa kluczowego. Ponadto, chociaż te słowniki opcji są nadal obsługiwane, każda z opcji indywidualnych w słowniku jest teraz obsługiwana jako argumenty jawnego słowa kluczowego.

* Hierarchia błędów jest teraz dziedziczona z `azure.core.AzureError` :

  * Zmieniono nazwę polecenia `HTTPFailure` na `CosmosHttpResponseError`
  * `JSONParseFailure` został usunięty i zastąpiony przez `azure.core.DecodeError`
  * Dodano dodatkowe błędy dla określonych kodów odpowiedzi:
    * `CosmosResourceNotFoundError` dla stanu 404
    * `CosmosResourceExistsError` dla stanu 409
    * `CosmosAccessConditionFailedError` dla stanu 412

* `CosmosClient` można teraz uruchomić w Menedżerze kontekstu, aby obsłużyć zamykanie połączenia z klientem.

* Odpowiedzi na Iterowanie (na przykład odpowiedzi na zapytania i odpowiedzi na liście) są teraz typu `azure.core.paging.ItemPaged` . Metoda `fetch_next_block` została zastąpiona przez iterator pomocniczy, do którego uzyskuje dostęp `by_page` Metoda.

### <a name="400b1"></a>4.0.0 B1

Wersja 4.0.0 B1 to pierwsza wersja zapoznawcza naszych działań w celu utworzenia przyjaznej dla użytkownika biblioteki klienta, która odpowiada najlepszym praktykom języka Python. Aby uzyskać więcej informacji o tym, i zapoznaj się z wersjami zapoznawczymi innych bibliotek zestawu Azure SDK, odwiedź stronę https://aka.ms/azure-sdk-preview1-python .

**Istotne zmiany: nowy projekt interfejsu API**

* Operacje są teraz ograniczone do określonego klienta:

  * `CosmosClient`: Ten klient obsługuje operacje na poziomie konta. Obejmuje to zarządzanie właściwościami usługi i wyświetlanie listy baz danych w ramach konta.
  * `Database`: Ten klient obsługuje operacje na poziomie bazy danych. Obejmuje to tworzenie i usuwanie kontenerów, użytkowników i procedur składowanych. Można uzyskać do niego dostęp z wystąpienia cosmosClient "według nazwy.
  * `Container`: Ten klient obsługuje operacje dla określonego kontenera. Obejmuje to wykonywanie zapytań i Wstawianie elementów oraz zarządzanie właściwościami.
  * `User`: Ten klient obsługuje operacje dla określonego użytkownika. Obejmuje to dodawanie i usuwanie uprawnień oraz zarządzanie właściwościami użytkowników.

    Dostęp do tych klientów można uzyskać, przechodząc do hierarchii klienta przy użyciu `get_<child>_client` metody. Aby uzyskać szczegółowe informacje na temat nowego interfejsu API, zobacz [dokumentację referencyjną](https://aka.ms/azsdk-python-cosmos-ref).

* Klienci są dostępni według nazwy, a nie według identyfikatora. Nie trzeba łączyć ciągów do tworzenia linków.

* Nie trzeba już importować typów i metod z poszczególnych modułów. Publiczny obszar powierzchniowy interfejsu API jest dostępny bezpośrednio w `azure.cosmos` pakiecie.

* Właściwości poszczególnych żądań można podać jako argumenty słów kluczowych zamiast konstruowania oddzielnego `RequestOptions` wystąpienia.

### <a name="302"></a>3.0.2

* Dodano obsługę elementu DataType MultiPolygon
* Poprawka błędu w zasadach ponawiania próby odczytu sesji
* Poprawka usterki dla nieprawidłowych problemów z uzupełnianiem podczas dekodowania podstawowych 64 ciągów

### <a name="301"></a>3.0.1

* Poprawka błędu w LocationCache
* Usterka logiki ponawiania punktu końcowego
* Stała dokumentacja

### <a name="300"></a>3.0.0

* Dodano obsługę zapisu dla wieloregionów
* Zmiany nazw
  * DocumentClient do CosmosClient
  * Kolekcja do kontenera
  * Dokument do elementu
  * Nazwa pakietu została zaktualizowana do "Azure-Cosmos"
  * Przestrzeń nazw została zaktualizowana do "Azure. Cosmos"

### <a name="233"></a>2.3.3

* Dodano obsługę serwera proxy
* Dodano obsługę kanału informacyjnego zmiany odczytu
* Dodano obsługę nagłówków przydziału kolekcji
* Poprawka dla problemu z tokenami dużej sesji
* Poprawka dla interfejsu API ReadMedia
* Poprawka w pamięci podręcznej zakresów kluczy partycji

### <a name="232"></a>2.3.2

* Dodano obsługę domyślnych ponownych prób w przypadku problemów z połączeniem.

### <a name="231"></a>ppkt

* Zaktualizowana dokumentacja dotycząca referencyjnego Azure Cosmos DB zamiast platformy Azure DocumentDB.

### <a name="230"></a>2.3.0

* Ta wersja zestawu SDK wymaga najnowszej wersji emulatora Azure Cosmos DB dostępnej do pobrania z programu https://aka.ms/cosmosdb-emulator .

### <a name="221"></a>2.2.1

* Poprawka dla agregacji DICT
* Poprawka do przycinania ukośników w łączu zasobów
* testy kodowania Unicode

### <a name="220"></a>2.2.0

* Dodano obsługę funkcji jednostki żądań na minutę (RU/m).
* Dodano obsługę nowego poziomu spójności o nazwie ConsistentPrefix.

### <a name="210"></a>2.1.0

* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i AVG).
* Dodano opcję wyłączania weryfikacji protokołu SSL w przypadku uruchamiania na emulatorze DocumentDB.
* Usunięto ograniczenie zależnego modułu żądań, aby dokładnie 2.10.0.
* Obniżona minimalna przepływność na partycjonowanych kolekcjach z 10 100 RU/s do 2500 RU/s.
* Dodano obsługę włączania rejestrowania skryptów podczas wykonywania procedury składowanej.
* Wersja interfejsu API REST dotrzymana do "2017-01-19" w tej wersji.

### <a name="201"></a>2.0.1

* Wprowadzono zmiany redakcyjne w komentarzach dokumentacji.

### <a name="200"></a>2.0.0

* Dodano obsługę języka Python 3,5.
* Dodano obsługę puli połączeń przy użyciu modułu żądania.
* Dodano obsługę spójności sesji.
* Dodano obsługę zapytań TOP/ORDERBY dla kolekcji partycjonowanych.

### <a name="190"></a>1.9.0

* Dodano obsługę zasad ponawiania dla żądań z ograniczeniami. (Żądania ograniczające żądania odbierają zbyt duży wyjątek, kod błędu 429). Domyślnie DocumentDB ponownych prób w przypadku każdego żądania w przypadku napotkania błędu o kodzie 429, ponieważ w nagłówku odpowiedzi zostanie osiągnięty czas retryAfter.
  Ustalony czas interwału ponowienia próby można teraz ustawić jako część właściwości RetryOptions obiektu ConnectionPolicy, jeśli chcesz zignorować czas retryAfter zwrócony przez serwer między ponownymi próbami.
  DocumentDB teraz czeka przez 30 sekund dla każdego żądania, które jest ograniczane (niezależnie od liczby ponownych prób) i zwraca odpowiedź z kodem błędu 429.
  Ten czas można również zastąpić we właściwości RetryOptions obiektu ConnectionPolicy.

* DocumentDB teraz zwraca wartość x-MS-dławienia-retry i licznik x-MS-dławienia-retry-Time-MS jako nagłówki odpowiedzi w każdym żądaniu, aby zauważyć licznik ponownych prób ograniczenia i skumulowany czas oczekiwania żądania między ponownymi próbami.

* Usunięto klasę RetryPolicy i odpowiadającą jej właściwość (retry_policy) uwidocznioną na klasie document_client i zamiast tego wprowadzamy klasę RetryOptions, która uwidacznia Właściwość RetryOptions w klasie ConnectionPolicy, która może być używana do przesłonięcia niektórych domyślnych opcji ponowień.

### <a name="180"></a>1.8.0

* Dodano obsługę kont baz danych replikowanych geograficznie.
* Poprawki testowe do przenoszenia hosta globalnego i masterKey do poszczególnych klas testowych.

### <a name="170"></a>1.7.0

* Dodano obsługę funkcji Time to Live (TTL) dla dokumentów.

### <a name="161"></a>1.6.1

* Poprawki błędów powiązane z partycjonowaniem po stronie serwera, aby zezwolić na znaki specjalne w ścieżce klucza partycji.

### <a name="160"></a>1.6.0

* Dodano obsługę funkcji kolekcje partycjonowane po stronie serwera.

### <a name="150"></a>1.5.0

* Dodano platformę fragmentowania po stronie klienta do zestawu SDK. Zaimplementowane klasy HashPartionResolver i RangePartitionResolver.

### <a name="142"></a>1.4.2

* Zaimplementuj upsert. Nowe metody UpsertXXX dodane do obsługi funkcji upsert.
* Zaimplementuj ID-Based Routing. Brak publicznych zmian interfejsu API, wszystkie zmiany wewnętrzne.

### <a name="130"></a>1.3.0

* Wersja pominięta, aby wprowadzić numer wersji w wyrównaniu z innymi zestawami SDK

### <a name="120"></a>1.2.0

* Obsługuje indeks geoprzestrzenny.
* Weryfikuje Właściwość identyfikatora dla wszystkich zasobów. Identyfikatory dla zasobów nie mogą zawierać `?, /, #, \\` znaków ani kończyć się spacją.
* Dodaje nowy nagłówek "postęp transformacji indeksu" do ResourceResponse.

### <a name="110"></a>1.1.0

* Implementuje zasady indeksowania v2

### <a name="101"></a>1.0.1

* Obsługuje połączenie serwera proxy

## <a name="release--retirement-dates"></a>Data wycofania &

Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** przed WYCOFANIEM zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji. Nowe funkcje i optymalizacje są dodawane tylko do bieżącego zestawu SDK, dlatego zaleca się, aby zawsze uaktualniać do najnowszej wersji zestawu SDK tak szybko, jak to możliwe.

> [!WARNING]
> Po dniu 31 sierpnia 2022 Azure Cosmos DB nie będą już naprawiać błędów ani nie zapewniać pomocy technicznej dla wersji 1. x i 2. x zestawu SDK języka Python Azure Cosmos DB dla interfejsu API SQL. Jeśli wolisz nie aktualizować, żądania wysyłane z wersji 1. x i 2. x zestawu SDK będą nadal obsługiwane przez usługę Azure Cosmos DB.

| Wersja | Data wydania | Data wycofania |
| --- | --- | --- |
| [4.2.0](#420) |Października 09, 2020 |--- |
| [4.1.0](#410) |10 sierpnia 2020 |--- |
| [4.0.0](#400) |20 maja 2020 |--- |
| [3.0.2](#302) |LIS 15, 2018 |--- |
| [3.0.1](#301) |04, 2018 |--- |
| [2.3.3](#233) |Września 08, 2018 |31 sierpnia 2022 |
| [2.3.2](#232) |8 maja 2018 r. |31 sierpnia 2022 |
| [2.3.1](#231) |21 grudnia 2017 |31 sierpnia 2022 |
| [2.3.0](#230) |10 listopada 2017 |31 sierpnia 2022 |
| [2.2.1](#221) |29 wrz 2017 |31 sierpnia 2022 |
| [2.2.0](#220) |10 maja 2017 |31 sierpnia 2022 |
| [2.1.0](#210) |01 maja, 2017 |31 sierpnia 2022 |
| [2.0.1](#201) |30 października 2016 |31 sierpnia 2022 |
| [2.0.0](#200) |29 września, 2016 |31 sierpnia 2022 |
| [1.9.0](#190) |07 lipca 2016 |31 sierpnia 2022 |
| [1.8.0](#180) |14 czerwca 2016 |31 sierpnia 2022 |
| [1.7.0](#170) |26 kwietnia 2016 |31 sierpnia 2022 |
| [1.6.1](#161) |08 kwietnia, 2016 |31 sierpnia 2022 |
| [1.6.0](#160) |29 marca 2016 |31 sierpnia 2022 |
| [1.5.0](#150) |03, 2016 |31 sierpnia 2022 |
| [1.4.2](#142) |06, 2015 |31 sierpnia 2022 |
| 1.4.1 |06, 2015 |31 sierpnia 2022 |
| [1.2.0](#120) |06 sierpnia 2015 |31 sierpnia 2022 |
| [1.1.0](#110) |09 lipca 2015 |31 sierpnia 2022 |
| [1.0.1](#101) |25 maja 2015 |31 sierpnia 2022 |
| 1.0.0 |07 kwietnia 2015 |31 sierpnia 2022 |
| 0.9.4 — przedlease |14 stycznia 2015 |29 lutego 2016 |
| 0.9.3 — przedlease |09, 2014 |29 lutego 2016 |
| 0.9.2 — przedlease |25 listopada 2014 |29 lutego 2016 |
| od 0.9.1 — przedlease |23 września, 2014 |29 lutego 2016 |
| 0.9.0 — przedlease |21 sierpnia 2014 |29 lutego 2016 |

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
