---
title: 'Azure Cosmos DB: interfejs API Node.js SQL, zestaw SDK & zasobów'
description: Dowiedz się wszystkiego o interfejsie API Node.js SQL i zestawie SDK, w tym o datach wydaniach, datach wycofania i zmianach wprowadzonych między poszczególnymi wersjami zestawu AZURE COSMOS DB Node.js SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: a3e21abe2f4ed24726256689af16b48ed6721ce8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366151"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK dla interfejsu API SQL: informacje o wersji i zasoby
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
> * [Moduł wykonywania zbiorczego — .NET 2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonywania zbiorczego — Java](sql-api-sdk-bulk-executor-java.md)

|Zasób  |Link  |
|---------|---------|
|Pobierz zestaw SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Dokumentacja interfejsu API  |  [Dokumentacja referencyjna zestawu JavaScript SDK](/javascript/api/%40azure/cosmos/)
|Instrukcje instalacji zestawu SDK  |  [Instrukcje instalacji](https://github.com/Azure/azure-sdk-for-js)
|Współtwomentuj zestaw SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Samples | [Node.js przykłady kodu](sql-api-nodejs-samples.md)
| Samouczek z wprowadzeniem | [Wprowadzenie do zestawu JAVAScript SDK](sql-api-nodejs-get-started.md)
| Samouczek dotyczący aplikacji internetowej | [Tworzenie Node.js internetowej przy użyciu Azure Cosmos DB](sql-api-nodejs-application.md)
| Bieżąca obsługiwana platforma | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) — zestaw SDK w wersji 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) — zestaw SDK w wersji 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) — zestaw SDK w wersji 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) — zestaw SDK w wersji 2.x.x<br/>[Node.js 4.2.0 —](https://nodejs.org/en/blog/release/v4.2.0/)ZESTAW SDK w wersji 1.x.x<br/> [Node.js v0.12 —](https://nodejs.org/en/blog/release/v0.12.0/)ZESTAW SDK w wersji 1.x.x<br/> [Node.js v0.10 —](https://nodejs.org/en/blog/release/v0.10.0/)zestaw SDK w wersji 1.x.x

## <a name="release-notes"></a>Informacje o wersji

### <a name="310"></a><a name="3.1.0"></a>3.1.0
* Ustaw wartość domyślną ResponseContinuationTokenLimitInKB na 1 kb. Domyślnie ograniczamy tę wartość do 1 KB, aby uniknąć długich nagłówków (Node.js ma globalny limit rozmiaru nagłówka). Użytkownik może ustawić to pole tak, aby zezwalało na dłuższe nagłówki, co może pomóc w zoptymalizowaniu wykonywania zapytań przez zaplecza.
* Usuń opcję disableSSLVerification. Ta opcja ma nowe alternatywy opisane [w](https://github.com/Azure/azure-cosmos-js/pull/388) #388

### <a name="304"></a><a name="3.0.4"></a>3.0.4
* Zezwalaj na jawne ustawienie nagłówka klucza partycji initialHeaders
* Użyj package.json#files, aby zapobiec publikacji dodatkowych plików
* Naprawienie błędu sortowania mapy routingu w starszej wersji węzła +v8
* Usunięto usterkę w przypadku, gdy użytkownik dostarcza opcje częściowego ponawiania prób

### <a name="303"></a><a name="3.0.3"></a>3.0.3
* Zapobieganie rozwiązywaniu przez pakiet webpack modułów wywoływanych za pomocą require

### <a name="302"></a><a name="3.0.2"></a>3.0.2
* Naprawiono długotrwałą usterkę, która zgłaszała, że dla zapytań agregowania zawsze była zgłaszana wartość 0

### <a name="300"></a><a name="3.0.0"></a>3.0.0

🎉 wersja 3! 🎉 wiele nowych funkcji, poprawek błędów i kilku zmian, które są istotne. Podstawowe cele tej wersji:

* Implementowanie najważniejszych nowych funkcji
  * ZAPYTANIA DISTINCT
  * Zapytania LIMIT/OFFSET
  * Żądania, które można anulować przez użytkownika
* Aktualizacja do najnowszej wersji interfejsu API REST usługi Cosmos, w której wszystkie kontenery mają nieograniczoną skalę
* Ułatwianie korzystania z usługi Cosmos z przeglądarki
* Lepsze dopasowanie do nowych wytycznych dotyczących zestawu Azure JS SDK

#### <a name="migration-guide-for-breaking-changes"></a>Przewodnik migracji dotyczący zmian, które są istotne
##### <a name="improved-client-constructor-options"></a>Ulepszone opcje konstruktora klienta

Opcje konstruktora zostały uproszczone:

* Nazwa klucza masterKey została zmieniona i przeniesiona na najwyższy poziom
* Właściwości wcześniej w obszarze options.auth zostały przeniesione na najwyższy poziom

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>Uproszczony interfejs API queryIterator
W wersji 2 istnieje wiele różnych sposobów iterowania lub pobierania wyników z zapytania. Próbowano uprościć interfejs API w wersji 3 i usunąć podobne lub zduplikowane interfejsy API:

* Usuń iterator.next() i iterator.current(). Użyj funkcji fetchNext(), aby pobrać strony wyników.
* Usuń iterator.forEach(). Zamiast tego użyj iteratorów asynchronicznych.
* iterator.exezmieniono nazwę na iterator.fetchNext()
* Nazwa iterator.toArray() została zmieniona na iterator.fetchAll()
* Strony są teraz prawidłowymi obiektami odpowiedzi zamiast zwykłymi obiektami JS
* const container = client.database(dbId).container(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>Stałe kontenery są teraz podzielone na partycje
Usługa Cosmos obsługuje teraz klucze partycji we wszystkich kontenerach, w tym tych, które zostały wcześniej utworzone jako stałe kontenery. Zestaw SDK w wersji 3 jest aktualizowany do najnowszej wersji interfejsu API, która implementuje tę zmianę, ale nie jest przerywana. Jeśli nie poimkniesz klucza partycji dla operacji, domyślnym kluczem systemu będzie klucz systemowy, który działa ze wszystkimi istniejącymi kontenerami i dokumentami.

##### <a name="upsert-removed-for-stored-procedures"></a>Usunięto operacji upsert dla procedur składowanych
Wcześniej można było uzyskać dostęp do operacji upsert dla kolekcji nie partycjonowanych, ale po zaktualizowaniu wersji interfejsu API wszystkie kolekcje są partycjonowane, więc całkowicie je usunęliśmy.

##### <a name="item-reads-will-not-throw-on-404"></a>Odczyty elementów nie będą zgłaszane przy numerze 404
const container = client.database(dbId).container(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Domyślny zapis w wielu regionach
Jeśli konfiguracja usługi Cosmos obsługuje tę funkcję, zestaw SDK domyślnie zapisuje dane w wielu regionach. Wcześniej było to zachowanie zgody.

##### <a name="proper-error-objects"></a>Prawidłowe obiekty błędów
Żądania nieudane teraz zgłaszają prawidłowy błąd lub podklasy błędu. Wcześniej wywłaszczyli zwykłe obiekty JS.

#### <a name="new-features"></a>Nowe funkcje
##### <a name="user-cancelable-requests"></a>Żądania, które można anulować przez użytkownika
Przeniesienie do pobrania wewnętrznie umożliwia użycie interfejsu API AbortController przeglądarki do obsługi operacji, które można anulować przez użytkownika. W przypadku operacji, w których wiele żądań jest potencjalnie w toku (takich jak zapytania między partycjami), wszystkie żądania dotyczące operacji zostaną anulowane. Nowi użytkownicy przeglądarki będą już mieć kontroler AbortController. Node.js użytkownicy będą musieli używać biblioteki polyfill

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Ustawianie przepływności w ramach operacji tworzenia bazy danych/kontenera
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
Generowanie tokenu nagłówka zostało podzielone na nową bibliotekę, @azure/cosmos-sign . Każda osoba wywołująca interfejs API REST usługi Cosmos może użyć tego interfejsu do podpisywania nagłówków przy użyciu tego samego kodu, który nazywamy wewnątrz obiektu @azure/cosmos .

##### <a name="uuid-for-generated-ids"></a>Identyfikator UUID dla wygenerowanych identyfikatorów
Wersja 2 miała niestandardowy kod do generowania identyfikatorów elementów. Przełączyliśmy się do dobrze znanej i utrzymywanej biblioteki społeczności uuid.

##### <a name="connection-strings"></a>Parametry połączeń
Teraz można przekazać ciąg połączenia skopiowany z Azure Portal:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Ulepszone środowisko przeglądarki
Chociaż możliwe było użycie zestawu SDK w wersji 2 w przeglądarce, nie było to idealne środowisko. Konieczne było wypełnienie kilku node.js wbudowanych bibliotek i użycie pakietu, takiego jak webpack lub Funkcji. Zestaw SDK w wersji 3 sprawia, że środowisko jest znacznie lepsze dla użytkowników przeglądarki.

* Zastępowanie wewnętrznych żądań pobieraniem (#245)
* Usuwanie użycia buforu (#330)
* Usuń użycie wbudowanego węzła na rzecz uniwersalnych pakietów/interfejsów API (#328)
* Przełącz się na kontroler węzła (#294)

#### <a name="bug-fixes"></a>Poprawki błędów
* Naprawianie testów ofert odczytu i powrotu (#224)
* Naprawiono błąd EnableEndpointDiscovery (#207)
* Rozwiązano problem z brakującymi wynikami podzielonymi na strony (#360)
* Rozwiń typ parametru zapytania SQL (#346)
* Dodawanie ttl do elementu ItemDefinition (#341)
* Poprawianie metryk zapytań cp (#311)
* Dodawanie activityId do feedResponse (#293)
* Przełączanie _ts typu z ciągu na liczbę (#252)(#295)
* Naprawiono agregację opłat za żądanie (#289)
* Zezwalaj na puste klucze partycji ciągów (#277)
* Dodawanie ciągu do typu zapytania powodującego konflikt (#237)
* Dodawanie wartości uniqueKeyPolicy do kontenera (#234)

#### <a name="engineering-systems"></a>Systemy inżynieryjne
Nie zawsze są to najbardziej widoczne zmiany, ale pomagają naszemu zespołowi w szybszym wysłaniu lepszego kodu.

* Używanie zbiorczych danych dla kompilacji produkcyjnych (#104)
* Aktualizacja do języka Typescript 3.5 (#327)
* Przekonwertuj na odwołania do projektu TS. Wyodrębnianie folderu testowego (#270)
* Włącz noUnusedLocals i noUnusedParameters (#275)
* Azure Pipelines YAML dla kompilacji ci (#298)

### <a name="215"></a><a name="2.1.5"></a>2.1.5
* Brak zmian w kodzie. Rozwiązano problem, który dotyczył niektórych dodatkowych plików zawartych w pakiecie 2.1.4.

### <a name="214"></a><a name="2.1.4"></a>2.1.4
* Naprawianie regionalnego trybu failover w ramach zasad ponawiania
* Poprawka właściwości ChangeFeed hasMoreResults
* Aktualizacje zależności dewelopera
* Dodaj PolicheckExclusions.txt

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Przełączanie _ts typu z ciągu na liczbę
* Naprawianie domyślnych testów indeksowania
* Zaimportuj ponownie uniqueKeyPolicy do wersji 2
* Demonstracyjne i demonstracyjne poprawki debugowania

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Poprawki oferty zadportuj z gałęzi v3
* Naprawiono usterkę w sygnaturze typu executeNext()
* Poprawki literówek

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Restrukturyzacja kompilacji. Umożliwia ściąganie wersji zestawu SDK w czasie kompilacji.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
#### <a name="new-features"></a>Nowe funkcje
* Dodano obsługę changefeed (#196)
* Dodano typ danych MultiPolygon na indeksowanie (#191)
* Dodaj właściwość "key" do konstruktora jako alias dla klucza masterKey (#202)

#### <a name="fixes"></a>Poprawki
* Naprawiono usterkę, która zwracała niepoprawną wartość iteratora przez wartość next()

#### <a name="engineering-improvements"></a>Ulepszenia inżynieryjne
* Dodawanie testu integracji na potrzeby użycia języka typescript (#199)
* Włączanie instalacji bezpośrednio z usługi GitHub (#194)

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* Dodaje interfejs dla typu agenta węzła. Użytkownicy języka TypeScript nie muszą już instalować @types/node jako zależność
* Preferowane lokalizacje są teraz prawidłowo honorowane
* Ulepszenia dotyczące współtworowania dokumentacji dla deweloperów
* Różne poprawki literówek

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* Rozwiązano problem z definicją typu wprowadzony w 2.0.3

### <a name="203"></a><a name="2.0.3"></a>2.0.3
* Usuwanie `big-integer` zależności
* Przejdź do dyrektyw referencyjnych dla typu AsyncIterable. Użytkownicy języka TypeScript nie muszą już dostosowywać ustawienia "lib".
* Poprawki literówek

### <a name="202"></a><a name="2.0.2"></a>2.0.2
* Naprawianie linków readme

### <a name="201"></a><a name="2.0.1"></a>2.0.1
* Naprawianie implementacji interfejsu ponawiania prób

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* GA of Version 2.0.0 of the JavaScript SDK
* Dodano obsługę zapisu w wielu regionach.

### <a name="200-3"></a><a name="2.0.0-3"></a>2.0.0-3
* WERSJA RC1 wersji 2.0.0 zestawu JAVAScript SDK dla publicznej wersji zapoznawczej.
* Nowy model obiektów z klasami CosmosClient najwyższego poziomu i metodami podzielonymi na odpowiednie klasy Database, Container i Item. 
* Obsługa [obietnic](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* Zestaw SDK przekonwertowany na język TypeScript.

### <a name="1144"></a><a name="1.14.4"></a>1.14.4
* Naprawiono dokumentację npm.

### <a name="1143"></a><a name="1.14.3"></a>1.14.3
* Dodano obsługę domyślnych ponownych prób w przypadku problemów z połączeniem.
* Dodano obsługę odczytywania zestawienia zmian kolekcji.
* Usunięto usterkę spójności sesji, która sporadycznie powodowała "sesja odczytu nie jest dostępna".
* Dodano obsługę metryk zapytań.
* Zmodyfikowano maksymalną liczbę połączeń agenta HTTP.

### <a name="1142"></a><a name="1.14.2"></a>1.14.2
* Zaktualizowano dokumentację w celu Azure Cosmos DB zamiast usługi Azure DocumentDB.
* Dodano obsługę ustawienia proxyUrl w connectionPolicy.

### <a name="1141"></a><a name="1.14.1"></a>1.14.1
* Drobna poprawka w systemach plików z wielkością liter.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Dodaje obsługę spójności sesji.
* Ta wersja zestawu SDK wymaga najnowszej wersji [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator).

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Dzielenie zapytań krzyżowych z weryfikacją partycji.
* Dodaje obsługę linku zasobu z wiodącymi i wiodącymi ukośników (i odpowiednimi testami).

### <a name="1122"></a><a name="1.12.2"></a>1.12.2
*    Naprawiono dokumentację npm.

### <a name="1121"></a><a name="1.12.1"></a>1.12.1
* Usunięto usterkę w funkcji executeStoredProcedure, która dotyczyła dokumentów ze specjalnymi znakami Unicode (LS, PS).
* Usunięto usterkę w obsłudze dokumentów ze znakami Unicode w kluczu partycji.
* Poprawiono obsługę tworzenia kolekcji z nośnikiem nazw. Problem z #114 GitHub.
* Naprawiono obsługę tokenu autoryzacji uprawnień. Problem z #178 GitHub.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Dodano obsługę nowego [poziomu spójności o](consistency-levels.md) nazwie ConsistentPrefix.
* Dodano obsługę UriFactory.
* Usunięto usterkę w obsłudze standardu Unicode. Problem z #171 GitHub.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i AVG).
* Dodano opcję kontrolowania stopnia równoległości zapytań między partycjami.
* Dodano opcję wyłączania weryfikacji TLS podczas uruchamiania w Azure Cosmos DB Emulator.
* Minimalna przepływność partycjonowanych kolekcji obniżyła się z 10 1000 RU/s do 2500 RU/s.
* Usunięto usterkę tokenu kontynuacji dla kolekcji z jedną partycją. Problem z #107 GitHub.
* Usunięto usterkę executeStoredProcedure podczas obsługi 0 jako pojedynczego param. Problemy z #155 GitHub.

### <a name="1102"></a><a name="1.10.2"></a>1.10.2
* Naprawiono nagłówek user-agent w celu dołączyć wersję zestawu SDK.
* Drobne oczyszczanie kodu.

### <a name="1101"></a><a name="1.10.1"></a>1.10.1
* Wyłączanie weryfikacji TLS w przypadku używania zestawu SDK do obsługi emulatora (hostname=localhost).
* Dodano obsługę włączania rejestrowania skryptów podczas wykonywania procedury składowanej.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Dodano obsługę zapytań równoległych między partycjami.
* Dodano obsługę zapytań TOP/ORDER BY dla kolekcji podzielonych na partycje.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Dodano obsługę zasad ponawiania dla żądań z ograniczeniami. (Żądania z ograniczeniami otrzymują wyjątek o zbyt dużej szybkości żądań, kod błędu 429). Domyślnie program Azure Cosmos DB ponawia próby dla każdego żądania po napotkaniu kodu błędu 429, honorując czas retryAfter w nagłówku odpowiedzi. Stały czas interwału ponawiania można teraz ustawić jako część właściwości RetryOptions obiektu ConnectionPolicy, jeśli chcesz zignorować czas retryAfter zwracany przez serwer między kolejnymi próbami. Azure Cosmos DB oczekuje teraz przez maksymalnie 30 sekund dla każdego żądania, które jest ograniczane (niezależnie od liczby ponownych prób) i zwraca odpowiedź z kodem błędu 429. Tym razem można również przesłonić właściwość RetryOptions obiektu ConnectionPolicy.
* Cosmos DB zwraca teraz nagłówki odpowiedzi x-ms-throttle-retry-count i x-ms-throttle-retry-wait-time-ms jako nagłówki odpowiedzi w każdym żądaniu w celu określenia liczby ponownych prób ograniczenia i skumulowanego czasu oczekiwania żądania między ponowieniami.
* Dodano klasę RetryOptions, ujawniając właściwość RetryOptions klasy ConnectionPolicy, która może służyć do zastępowania niektórych domyślnych opcji ponawiania.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Dodano obsługę kont bazy danych w wielu regionach.

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Dodano obsługę funkcji czasu wygaśnięcia (TTL) dla dokumentów.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Zaimplementowano [kolekcje partycjonowane](partitioning-overview.md) [i zdefiniowane przez użytkownika poziomy wydajności.](performance-levels.md)

### <a name="156"></a><a name="1.5.6"></a>1.5.6
* Usunięto usterkę RangePartitionResolver.resolveForRead, która nie zwracała linków z powodu złego połączenia wyników.

### <a name="155"></a><a name="1.5.5"></a>1.5.5
* Naprawiono element hashPartitionResolver resolveForRead(): gdy żaden podany klucz partycji nie zgłaszał wyjątku, zamiast zwracać listę wszystkich zarejestrowanych linków.

### <a name="154"></a><a name="1.5.4"></a>1.5.4
* Rozwiązano problem [#100](https://github.com/Azure/azure-documentdb-node/issues/100) — dedykowany agent HTTPS: Unikaj modyfikowania agenta globalnego do Azure Cosmos DB celów. Użyj dedykowanego agenta dla wszystkich żądań biblioteki.

### <a name="153"></a><a name="1.5.3"></a>1.5.3
* Rozwiązano problem [#81](https://github.com/Azure/azure-documentdb-node/issues/81) — poprawna obsługa łączników w identyfikatorach nośników.

### <a name="152"></a><a name="1.5.2"></a>1.5.2
* Rozwiązano [problem](https://github.com/Azure/azure-documentdb-node/issues/95) #95 — ostrzeżenie o wycieku odbiornika EventEmitter.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Rozwiązano [problem](https://github.com/Azure/azure-documentdb-node/issues/90) #92 — zmiana nazwy skrótu folderu na skrót w systemach z wielkość liter.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Zaim implementuj obsługę fragmentowania, dodając & rozpoznawania partycji zakresu skrótów.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementowanie operacji upsert. Nowe metody upsertXXX w documentClient.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Pominięto w celu wyrównania numerów wersji z innymi zestawami SDK.

### <a name="122"></a><a name="1.2.2"></a>1.2.2
* Podziel otokę obietnic Q na nowe repozytorium.
* Zaktualizuj plik pakietu do rejestr npm.

### <a name="121"></a><a name="1.2.1"></a>1.2.1
* Implementuje routing oparty na identyfikatorach.
* Rozwiązano problem [#49](https://github.com/Azure/azure-documentdb-node/issues/49) — bieżąca właściwość powoduje konflikt z metodą current().

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Dodano obsługę indeksu geoprzestrzennego.
* Weryfikuje właściwość ID dla wszystkich zasobów. Identyfikatory zasobów nie mogą zawierać znaków ?, /, #,  &#47;&#47;, znaków ani kończyć spacją.
* Dodaje nowy nagłówek "postęp przekształcania indeksu" do klasy ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementuje zasady indeksowania w wersji 2.

### <a name="103"></a><a name="1.0.3"></a>1.0.3
* Problem [#40](https://github.com/Azure/azure-documentdb-node/issues/40) — zaimplementowano konfiguracje z elementami core i promise SDK.

### <a name="102"></a><a name="1.0.2"></a>1.0.2
* Problem [#45](https://github.com/Azure/azure-documentdb-node/issues/45) — otoka obietnic nie zawiera nagłówka z błędem.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Zaimplementowano możliwość wykonywania zapytań o konflikty przez dodanie funkcji readConflicts, readConflictAsync i queryConflicts.
* Zaktualizowana dokumentacja interfejsu API.
* Problem [#41](https://github.com/Azure/azure-documentdb-node/issues/41) — błąd client.createDocumentAsync.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* Zestaw SDK ga ga.

## <a name="release--retirement-dates"></a>Daty wycofania & wersji

Firma Microsoft udostępnia powiadomienie co najmniej **12 miesięcy** przed wycofaniem zestawu SDK, aby ułatwić przejście do nowszej/obsługiwanej wersji. Nowe funkcje i optymalizacje są dodawane tylko do bieżącego zestawu SDK, dlatego zaleca się, aby zawsze uaktualniać do najnowszej wersji zestawu SDK tak szybko, jak to możliwe.

| Wersja | Data wydania | Data wycofania |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26 lipca 2019 r. |--- |
| [3.0.4](#3.0.4) |22 lipca 2019 r. |--- |
| [3.0.3](#3.0.3) |17 lipca 2019 r. |--- |
| [3.0.2](#3.0.2) |9 lipca 2019 r. |--- |
| [3.0.0](#3.0.0) |28 czerwca 2019 r. |--- |
| [2.1.5](#2.1.5) |20 marca 2019 r. |--- |
| [2.1.4](#2.1.4) |15 marca 2019 r. |--- |
| [2.1.3](#2.1.3) |8 marca 2019 r. |--- |
| [2.1.2](#2.1.2) |28 stycznia 2019 r. |--- |
| [2.1.1](#2.1.1) |5 grudnia 2018 r. |--- |
| [2.1.0](#2.1.0) |4 grudnia 2018 r. |--- |
| [2.0.5](#2.0.5) |7 listopada 2018 r. |--- |
| [2.0.4](#2.0.4) |30 października 2018 r. |--- |
| [2.0.3](#2.0.3) |30 października 2018 r. |--- |
| [2.0.2](#2.0.2) |10 października 2018 r. |--- |
| [2.0.1](#2.0.1) |25 września 2018 r. |--- |
| [2.0.0](#2.0.0) |Wrzesień 24, 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 sierpnia 2018 r. |--- |
| [1.14.4](#1.14.4) |3 maja 2018 r. |30 sierpnia 2020 r. |
| [1.14.3](#1.14.3) |3 maja 2018 r. |30 sierpnia 2020 r. |
| [1.14.2](#1.14.2) |21 grudnia 2017 r. |30 sierpnia 2020 r. |
| [1.14.1](#1.14.1) |10 listopada 2017 r. |30 sierpnia 2020 r. |
| [1.14.0](#1.14.0) |9 listopada 2017 r. |30 sierpnia 2020 r. |
| [1.13.0](#1.13.0) |11 października 2017 r. |30 sierpnia 2020 r. |
| [1.12.2](#1.12.2) |10 sierpnia 2017 r. |30 sierpnia 2020 r. |
| [1.12.1](#1.12.1) |10 sierpnia 2017 r. |30 sierpnia 2020 r. |
| [1.12.0](#1.12.0) |10 maja 2017 |30 sierpnia 2020 r. |
| [1.11.0](#1.11.0) |16 marca 2017 r. |30 sierpnia 2020 r. |
| [1.10.2](#1.10.2) |27 stycznia 2017 r. |30 sierpnia 2020 r. |
| [1.10.1](#1.10.1) |22 grudnia 2016 r. |30 sierpnia 2020 r. |
| [1.10.0](#1.10.0) |3 października 2016 r. |30 sierpnia 2020 r. |
| [1.9.0](#1.9.0) |7 lipca 2016 r. |30 sierpnia 2020 r. |
| [1.8.0](#1.8.0) |14 czerwca 2016 r. |30 sierpnia 2020 r. |
| [1.7.0](#1.7.0) |26 kwietnia 2016 |30 sierpnia 2020 r. |
| [1.6.0](#1.6.0) |29 marca 2016 r. |30 sierpnia 2020 r. |
| [1.5.6](#1.5.6) |8 marca 2016 r. |30 sierpnia 2020 r. |
| [1.5.5](#1.5.5) |2 lutego 2016 r. |30 sierpnia 2020 r. |
| [1.5.4](#1.5.4) |1 lutego 2016 r. |30 sierpnia 2020 r. |
| [1.5.2](#1.5.2) |26 stycznia 2016 r. |30 sierpnia 2020 r. |
| [1.5.2](#1.5.2) |22 stycznia 2016 r. |30 sierpnia 2020 r. |
| [1.5.1](#1.5.1) |4 stycznia 2016 r. |30 sierpnia 2020 r. |
| [1.5.0](#1.5.0) |31 grudnia 2015 r. |30 sierpnia 2020 r. |
| [1.4.0](#1.4.0) |6 października 2015 r. |30 sierpnia 2020 r. |
| [1.3.0](#1.3.0) |6 października 2015 r. |30 sierpnia 2020 r. |
| [1.2.2](#1.2.2) |10 września 2015 r. |30 sierpnia 2020 r. |
| [1.2.1](#1.2.1) |15 sierpnia 2015 r. |30 sierpnia 2020 r. |
| [1.2.0](#1.2.0) |5 sierpnia 2015 r. |30 sierpnia 2020 r. |
| [1.1.0](#1.1.0) |9 lipca 2015 r. |30 sierpnia 2020 r. |
| [1.0.3](#1.0.3) |4 czerwca 2015 r. |30 sierpnia 2020 r. |
| [1.0.2](#1.0.2) |23 maja 2015 r. |30 sierpnia 2020 r. |
| [1.0.1](#1.0.1) |15 maja 2015 r. |30 sierpnia 2020 r. |
| [1.0.0](#1.0.0) |8 kwietnia 2015 r. |30 sierpnia 2020 r. |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).