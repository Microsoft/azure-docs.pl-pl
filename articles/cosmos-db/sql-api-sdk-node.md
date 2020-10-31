---
title: 'Azure Cosmos DB: SQL Node.js API, zasoby & SDK'
description: Poznaj wszystkie informacje o interfejsie API Node.js SQL i zestawie SDK, w tym daty wydania, daty wycofania i zmiany wprowadzone między poszczególnymi wersjami Azure Cosmos DB Node.js SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: 7f101097945e5f345bae351ef5ca2a23a3e14be6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091162"
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
> * [Łącznik platformy Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Moduł wykonywania zbiorczego — .NET 2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonywania zbiorczego — Java](sql-api-sdk-bulk-executor-java.md)

|Zasób  |Łącze  |
|---------|---------|
|Pobierz zestaw SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Dokumentacja interfejsu API  |  [Dokumentacja referencyjna języka JavaScript SDK](/javascript/api/%40azure/cosmos/?preserve-view=true&view=azure-node-latest)
|Instrukcje dotyczące instalacji zestawu SDK  |  [Instrukcje instalacji](https://github.com/Azure/azure-sdk-for-js)
|Współtworzenie zestawu SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Samples | [ Przykłady koduNode.js](sql-api-nodejs-samples.md)
| Wprowadzenie — samouczek | [Wprowadzenie do zestawu SDK języka JavaScript](sql-api-nodejs-get-started.md)
| Samouczek aplikacji sieci Web | [Tworzenie aplikacji sieci Web Node.js przy użyciu Azure Cosmos DB](sql-api-nodejs-application.md)
| Bieżąca obsługiwana platforma | [Node.js V12. x](https://nodejs.org/en/blog/release/v12.7.0/) -SDK w wersji 3. x. x<br/>[Node.js v10. x](https://nodejs.org/en/blog/release/v10.6.0/) -SDK w wersji 3. x. x<br/>[Node.js V8. x](https://nodejs.org/en/blog/release/v8.16.0/) -SDK w wersji 3. x. x<br/>[Node.js V6. x](https://nodejs.org/en/blog/release/v6.10.3/) -SDK w wersji 2. x. x<br/>[Node.js v 4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)— zestaw SDK w wersji 1. x. x<br/> [Node.js v 0.12](https://nodejs.org/en/blog/release/v0.12.0/)— zestaw SDK w wersji 1. x. x<br/> [Node.js v 0.10](https://nodejs.org/en/blog/release/v0.10.0/)— zestaw SDK w wersji 1. x. x

## <a name="release-notes"></a>Informacje o wersji

### <a name="310"></a><a name="3.1.0"></a>3.1.0
* Ustaw wartość domyślną ResponseContinuationTokenLimitInKB na rozmiarze 1 KB. Domyślnie są one ograniczone do rozmiarze 1 KB, aby uniknąć długich nagłówków (Node.js ma limit rozmiaru nagłówka globalnego). Użytkownik może ustawić to pole, aby zezwalać na dłuższe nagłówki, co może pomóc w zaplecze zoptymalizować wykonywanie zapytania.
* Usuń disableSSLVerification. Ta opcja ma nowe alternatywy opisane w [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name="304"></a><a name="3.0.4"></a>3.0.4
* Zezwalaj initialHeaders na jawne Ustawianie nagłówka klucza partycji
* Użyj package.jsw # plikach, aby zapobiec publikowaniu plików obcych
* Napraw błąd sortowania mapy routingu w starszej wersji węzła i V8
* Naprawia usterkę, gdy użytkownik dostarcza opcje częściowej ponownej próby

### <a name="303"></a><a name="3.0.3"></a>3.0.3
* Nie Zezwalaj na pakiet WebPack z rozpoznawania modułów o nazwie with

### <a name="302"></a><a name="3.0.2"></a>3.0.2
* Rozwiązuje długotrwałą usterkę, w której jednostek ru były zawsze zgłaszane jako 0 dla zapytań agregowanych

### <a name="300"></a><a name="3.0.0"></a>3.0.0

🎉 Wersja V3! 🎉 wiele nowych funkcji, poprawek błędów i kilku istotnych zmian. Główne cele tej wersji:

* Zaimplementuj najważniejsze nowe funkcje
  * Zapytania ODRĘBNe
  * Zapytania OGRANICZAjące/przesunięcia
  * Żądania anulowane przez użytkownika
* Aktualizacja do najnowszej wersji interfejsu API REST Cosmos, w której wszystkie kontenery mają nieograniczone skalowanie
* Ułatw korzystanie z Cosmos z przeglądarki
* Lepsze dostosowanie przy użyciu nowych wytycznych dotyczących zestawu SDK platformy Azure

#### <a name="migration-guide-for-breaking-changes"></a>Przewodnik migracji dotyczący istotnych zmian
##### <a name="improved-client-constructor-options"></a>Ulepszone opcje konstruktora klienta

Opcje konstruktora zostały uproszczone:

* masterKey zmieniono nazwę klucza i został przeniesiony do najwyższego poziomu
* Właściwości wcześniej w obszarze Options. auth zostały przeniesione do najwyższego poziomu

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

##### <a name="simplified-queryiterator-api"></a>Uproszczony interfejs API QueryIterator
W wersji 2 istniały wiele różnych sposobów iteracji lub pobierania wyników zapytania. Podjęto próbę uproszczenia interfejsu API v3 i usunięcia podobnych lub zduplikowanych interfejsów API:

* Usuń iterator. Next () i iterator. Current (). Użyj fetchNext (), aby uzyskać strony wyników.
* Usuń iterator. forEach (). Zamiast tego użyj iteratorów asynchronicznych.
* iterator.executeNext () zmieniono nazwę na iterator. fetchNext ()
* Nazwa iterator. ToArray — () została zmieniona na iterator. fetchAll ()
* Strony są teraz prawidłowymi obiektami odpowiedzi zamiast zwykłych obiektów JS
* const Container = Client. Database (dbId). Container (containerId)

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
Usługa Cosmos obsługuje teraz klucze partycji we wszystkich kontenerach, w tym te, które zostały wcześniej utworzone jako stałe kontenery. Zestaw SDK V3 jest aktualizowany do najnowszej wersji interfejsu API, która implementuje tę zmianę, ale nie jest przerywana. Jeśli nie podasz klucza partycji dla operacji, domyślnie zostanie używany klucz systemowy, który działa ze wszystkimi istniejącymi kontenerami i dokumentami.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert usunięto dla procedur składowanych
Wcześniej upsert było dozwolone dla kolekcji niepodzielonych na partycje, ale z aktualizacją wersji interfejsu API wszystkie kolekcje są podzielone na partycje, aby zostały całkowicie usunięte.

##### <a name="item-reads-will-not-throw-on-404"></a>Odczyty elementów nie będą zgłaszane na 404
const Container = Client. Database (dbId). Container (containerId)

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

##### <a name="default-multi-region-write"></a>Domyślny zapis dla wieloregionu
Zestaw SDK będzie teraz zapisywać w wielu regionach domyślnie, jeśli jego konfiguracja Cosmos obsługuje tę funkcję. Ta funkcja była wcześniej stosowana.

##### <a name="proper-error-objects"></a>Poprawne obiekty błędów
Żądania zakończone niepowodzeniem teraz zwracają prawidłowy błąd lub podklasy błędu. Wcześniej wygenerowały obiekty zwykłej JS.

#### <a name="new-features"></a>Nowe funkcje
##### <a name="user-cancelable-requests"></a>Żądania anulowane przez użytkownika
Funkcja przenoszenia do pobrania wewnętrznie pozwala nam korzystać z interfejsu API AbortController przeglądarki w celu obsługi operacji z możliwością anulowania użytkownika. W przypadku operacji, w których wiele żądań jest potencjalnie w toku (na przykład zapytań między partycjami), wszystkie żądania dla operacji zostaną anulowane. Użytkownicy nowoczesnej przeglądarki będą już mieć AbortController. Node.js użytkownicy będą musieli używać biblioteki z wypełnieniem

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
Generowanie tokenu nagłówka zostało podzielone na nową bibliotekę, @azure/cosmos-sign . Każda osoba, która wywołuje interfejs API REST Cosmos, bezpośrednio może używać tego samego kodu do podpisywania nagłówków @azure/cosmos

##### <a name="uuid-for-generated-ids"></a>Identyfikator UUID dla wygenerowanych identyfikatorów
V2 ma niestandardowy kod generujący identyfikatory elementów. Przełączonomy do dobrze znanego i obsługiwanego identyfikatora UUID biblioteki społeczności.

##### <a name="connection-strings"></a>Parametry połączeń
Teraz można przekazać parametry połączenia skopiowane z Azure Portal:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Udoskonalone środowisko przeglądarki
Chociaż było możliwe użycie zestawu v2 SDK w przeglądarce, nie jest to idealne środowisko. Musisz wypełnić kilka node.js wbudowanych bibliotek i użyć pakietu, takiego jak WebPack lub Parcel. Zestaw v3 SDK sprawia, że środowisko pracy jest znacznie lepsze dla użytkowników przeglądarki.

* Zastąp elementy wewnętrzne żądania za pomocą pobrania (#245)
* Usuń użycie buforu (#330)
* Usuń użycie wbudowanego węzła na rzecz uniwersalnych pakietów/interfejsów API (#328)
* Przełącz do węzła-Abort-Controller (#294)

#### <a name="bug-fixes"></a>Poprawki błędów
* Poprawka oferty Odczytaj i wróć oferty (#224)
* Poprawka EnableEndpointDiscovery (#207)
* Naprawianie brakujących jednostek RU w wynikach z podziałem na strony (#360)
* Rozwiń typ parametru zapytania SQL (#346)
* Dodaj czas wygaśnięcia do ItemDefinition (#341)
* Usuń metryki zapytania CP (#311)
* Dodaj activityId do FeedResponse (#293)
* Przełącz typ _ts z ciągu na liczbę (#252) (#295)
* Napraw agregację opłaty żądania (#289)
* Zezwalaj na puste klucze partycji ciągu (#277)
* Dodaj ciąg do typu zapytania powodującego konflikt (#237)
* Dodaj uniqueKeyPolicy do kontenera (#234)

#### <a name="engineering-systems"></a>Systemy inżynieryjne
Nie zawsze są to najbardziej widoczne zmiany, ale pomagają naszym zespołowi w szybszym dostarczaniu kodu.

* Użyj pakietu zbiorczego dla kompilacji produkcyjnych (#104)
* Aktualizacja do języka TypeScript 3,5 (#327)
* Konwertuj na odwołania do projektu TS. Wyodrębnij folder testowy (#270)
* Włącz noUnusedLocals i noUnusedParameters (#275)
* Azure Pipelines YAML dla kompilacji CI (#298)

### <a name="215"></a><a name="2.1.5"></a>ppkt
* Nie wprowadzono żadnych zmian w kodzie. Rozwiązuje problem polegający na tym, że niektóre dodatkowe pliki zostały uwzględnione w pakiecie 2.1.4.

### <a name="214"></a><a name="2.1.4"></a>2.1.4
* Naprawa regionalnego trybu failover w ramach zasad ponawiania
* Napraw Właściwość ChangeFeed hasMoreResults
* Aktualizacje zależności dev
* Dodaj PolicheckExclusions.txt

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Przełącz typ _ts z ciągu na liczbę
* Napraw domyślne testy indeksowania
* Backport uniqueKeyPolicy do wersji 2
* Poprawki debugowania demonstracyjnego i demonstracyjnego

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Poprawki oferty backport z gałęzi v3
* Usuwanie usterki w sygnaturze typu executeNext ()
* Poprawki pisowni

### <a name="211"></a><a name="2.1.1"></a>pkt
* Restrukturyzacja kompilacji. Zezwala na ściąganie wersji zestawu SDK w czasie kompilacji.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
#### <a name="new-features"></a>Nowe funkcje
* Dodano obsługę ChangeFeed (#196)
* Dodano MultiPolygon DataType dla indeksowania (#191)
* Dodaj właściwość "Key" do konstruktora jako alias dla masterKey (#202)

#### <a name="fixes"></a>Poprawki
* Usuń usterkę, gdzie Next () zwróciła niepoprawną wartość w iterator

#### <a name="engineering-improvements"></a>Ulepszenia inżynieryjne
* Dodaj test integracji dla użycia TypeScript (#199)
* Włącz instalację bezpośrednio z usługi GitHub (#194)

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* Dodaje interfejs dla typu agenta węzła. Użytkownicy języka TypeScript nie muszą już być instalowane @types/node jako zależność
* Preferowane lokalizacje są teraz prawidłowo honorowane
* Ulepszenia dotyczące tworzenia dokumentacji dla deweloperów
* Różne poprawki pisowni

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* Rozwiązano problem z definicją typu wprowadzony w 2.0.3

### <a name="203"></a><a name="2.0.3"></a>2.0.3
* Usuń `big-integer` zależność
* Przejdź do dyrektyw referencyjnych dla typu AsyncIterable. Użytkownicy języka TypeScript nie muszą już dostosowywać ustawienia "lib".
* Poprawki pisowni

### <a name="202"></a><a name="2.0.2"></a>2.0.2
* Napraw linki Readme

### <a name="201"></a><a name="2.0.1"></a>2.0.1
* Popraw implementację interfejsu ponawiania próby

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Wersja 2.0.0 zestawu JavaScript SDK
* Dodano obsługę zapisu dla wieloregionu.

### <a name="200-3"></a><a name="2.0.0-3"></a>2.0.0-3
* RC1 wersja 2.0.0 zestawu JavaScript SDK dla publicznej wersji zapoznawczej.
* Nowy model obiektów z CosmosClient i metodami najwyższego poziomu, które dzielą się między odpowiednimi klasami baz danych, kontenerów i elementów. 
* Obsługa [niesie obietnice zwiększenia](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* Zestaw SDK przekonwertowany na język TypeScript.

### <a name="1144"></a><a name="1.14.4"></a>1.14.4
* npm dokumentacja.

### <a name="1143"></a><a name="1.14.3"></a>1.14.3
* Dodano obsługę domyślnych ponownych prób w przypadku problemów z połączeniem.
* Dodano obsługę odczytywania źródła zmian kolekcji.
* Rozwiązano błąd spójności sesji, który sporadycznie spowodował niedostępną sesję odczytu.
* Dodano obsługę metryk zapytań.
* Zmodyfikowano maksymalną liczbę połączeń agenta http.

### <a name="1142"></a><a name="1.14.2"></a>1.14.2
* Zaktualizowana dokumentacja dotycząca referencyjnego Azure Cosmos DB zamiast platformy Azure DocumentDB.
* Dodano obsługę ustawienia proxyUrl w ConnectionPolicy.

### <a name="1141"></a><a name="1.14.1"></a>1.14.1
* Drobna poprawka dla systemów plików z uwzględnieniem wielkości liter.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Dodaje obsługę spójności sesji.
* Ta wersja zestawu SDK wymaga najnowszej wersji [emulatora Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Podziel zapytania obejmujące wiele partycji.
* Dodaje obsługę dla linku zasobu z wiodącymi i końcowymi ukośnikami (i odpowiednimi testami).

### <a name="1122"></a><a name="1.12.2"></a>1.12.2
*    npm dokumentacja.

### <a name="1121"></a><a name="1.12.1"></a>1.12.1
* Naprawiono usterkę w executeStoredProcedure, gdzie dokumenty mają specjalne znaki Unicode (LS, PS).
* Naprawiono usterkę w obsłudze dokumentów ze znakami Unicode w kluczu partycji.
* Stała obsługa tworzenia kolekcji z nazwą nośnika. #114 problemów z usługą GitHub.
* Stała obsługa tokenu autoryzacji uprawnień. #178 problemów z usługą GitHub.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Dodano obsługę nowego [poziomu spójności](consistency-levels.md) o nazwie ConsistentPrefix.
* Dodano obsługę UriFactory.
* Rozwiązano błąd obsługi Unicode. #171 problemów z usługą GitHub.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i AVG).
* Dodano opcję kontrolującą stopień równoległości zapytań między partycjami.
* Dodano opcję wyłączania weryfikacji protokołu TLS podczas uruchamiania z emulatorem Azure Cosmos DB.
* Obniżona minimalna przepływność na partycjonowanych kolekcjach z 10 100 RU/s do 2500 RU/s.
* Naprawiono usterkę tokenu kontynuacji dla kolekcji z jedną partycją. #107 problemów z usługą GitHub.
* Rozwiązano błąd executeStoredProcedure w obsłudze 0 jako pojedynczy PARAM. #155 problemów z usługą GitHub.

### <a name="1102"></a><a name="1.10.2"></a>1.10.2
* Stały nagłówek User-Agent do uwzględnienia wersji zestawu SDK.
* Oczyszczanie kodu pomocniczego.

### <a name="1101"></a><a name="1.10.1"></a>1.10.1
* Wyłączanie weryfikacji protokołu TLS w przypadku używania zestawu SDK do kierowania emulatora (hostname = localhost).
* Dodano obsługę włączania rejestrowania skryptów podczas wykonywania procedury składowanej.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Dodano obsługę zapytań równoległych między partycjami.
* Dodano obsługę funkcji TOP/ORDER przez zapytania dotyczące kolekcji partycjonowanych.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Dodano obsługę zasad ponawiania dla żądań z ograniczeniami. (Żądania ograniczające żądania odbierają zbyt duży wyjątek, kod błędu 429). Domyślnie program Azure Cosmos DB ponownych prób w przypadku każdego żądania, gdy zostanie napotkany kod błędu 429, a w nagłówku odpowiedzi zostanie osiągnięty czas retryAfter. Ustalony czas interwału ponowienia próby można teraz ustawić jako część właściwości RetryOptions obiektu ConnectionPolicy, jeśli chcesz zignorować czas retryAfter zwrócony przez serwer między ponownymi próbami. Azure Cosmos DB teraz czeka przez maksymalnie 30 sekund dla każdego żądania, które jest ograniczane (niezależnie od liczby ponownych prób) i zwraca odpowiedź z kodem błędu 429. Ten czas można również zastąpić we właściwości RetryOptions obiektu ConnectionPolicy.
* Cosmos DB teraz zwraca wartość x-MS-dławienia-retry i licznik x-MS-dławienia-retry-Time-MS jako nagłówki odpowiedzi w każdym żądaniu, aby zauważyć licznik ponownych prób ograniczenia i skumulowany czas oczekiwania żądania między ponownymi próbami.
* Dodano klasę RetryOptions, która uwidacznia Właściwość RetryOptions klasy ConnectionPolicy, która może służyć do przesłonięcia niektórych domyślnych opcji ponowień.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Dodano obsługę wieloregionowych kont baz danych.

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Dodano obsługę funkcji Time to Live (TTL) dla dokumentów.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Wdrożone [kolekcje partycjonowane](partitioning-overview.md) i [poziomy wydajności zdefiniowane przez użytkownika](performance-levels.md).

### <a name="156"></a><a name="1.5.6"></a>1.5.6
* Naprawiono błąd RangePartitionResolver. resolveForRead, który nie zwraca linków z powodu nieprawidłowej kombinacji wyników.

### <a name="155"></a><a name="1.5.5"></a>1.5.5
* Stała hashPartitionResolver resolveForRead (): Jeśli nie podano klucza partycji, który nie zwrócił wyjątku, zamiast zwracać listę wszystkich zarejestrowanych linków.

### <a name="154"></a><a name="1.5.4"></a>1.5.4
* Rozwiązywanie problemów z [#100](https://github.com/Azure/azure-documentdb-node/issues/100) — dedykowany Agent https: Unikaj modyfikowania agenta globalnego Azure Cosmos DB celach. Użyj dedykowanego agenta dla wszystkich żądań biblioteki lib.

### <a name="153"></a><a name="1.5.3"></a>1.5.3
* Rozwiązywanie problemów [#81](https://github.com/Azure/azure-documentdb-node/issues/81) — prawidłowo obsłudze łączników w identyfikatorach multimediów.

### <a name="152"></a><a name="1.5.2"></a>1.5.2
* Rozwiązuje problem [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -ostrzeżenie przecieku odbiornika EventEmitter.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Rozwiązuje problem [#92](https://github.com/Azure/azure-documentdb-node/issues/90) — Zmień wartość skrótu folderu na hash w przypadku systemów z uwzględnieniem wielkości liter.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Implementowanie obsługi fragmentowania przez dodanie elementów rozpoznawania partycji zakresu & skrótu.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Zaimplementuj upsert. Nowe metody upsertXXX na documentClient.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Pominięto, aby wprowadzić numery wersji w wyrównaniu z innymi zestawami SDK.

### <a name="122"></a><a name="1.2.2"></a>ppkt
* Podziel otokę Q niesie obietnice zwiększenia na nowe repozytorium.
* Zaktualizuj do pliku pakietu dla rejestr npm.

### <a name="121"></a><a name="1.2.1"></a>1.2.1
* Implementuje routing oparty na IDENTYFIKATORach.
* Problem z poprawkami [#49](https://github.com/Azure/azure-documentdb-node/issues/49) — bieżąca właściwość powoduje konflikt z bieżącą metodą ().

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Dodano obsługę indeksów geoprzestrzennych.
* Weryfikuje Właściwość identyfikatora dla wszystkich zasobów. Identyfikatory dla zasobów nie mogą zawierać znaku?,/, #,  &#47;&#47;, znaków ani kończyć się spacją.
* Dodaje nowy nagłówek "postęp transformacji indeksu" do ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementuje zasady indeksowania w wersji 2.

### <a name="103"></a><a name="1.0.3"></a>1.0.3
* Problem z zaimplementowanymi [#40](https://github.com/Azure/azure-documentdb-node/issues/40) i konfiguracjami grunt w zestawie SDK i Promise.

### <a name="102"></a><a name="1.0.2"></a>1.0.2
* Problem [#45](https://github.com/Azure/azure-documentdb-node/issues/45) — otoka niesie obietnice zwiększenia nie zawiera nagłówka z błędem.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Zaimplementowana możliwość wykonywania zapytań dotyczących konfliktów przez dodawanie readConflicts, readConflictAsync i queryConflicts.
* Zaktualizowana dokumentacja interfejsu API.
* Problem [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -Client. createDocumentAsync.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* ZESTAW SDK.

## <a name="release--retirement-dates"></a>Data wycofania &

Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** przed WYCOFANIEM zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji. Nowe funkcje i optymalizacje są dodawane tylko do bieżącego zestawu SDK, dlatego zaleca się, aby zawsze uaktualniać do najnowszej wersji zestawu SDK tak szybko, jak to możliwe.

| Wersja | Data wydania | Data wycofania |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26 lipca 2019 |--- |
| [3.0.4](#3.0.4) |22 lipca 2019 |--- |
| [3.0.3](#3.0.3) |17 lipca 2019 |--- |
| [3.0.2](#3.0.2) |9 lipca 2019 r. |--- |
| [3.0.0](#3.0.0) |28 czerwca 2019 |--- |
| [ppkt](#2.1.5) |20 marca 2019 |--- |
| [2.1.4](#2.1.4) |15 marca 2019 |--- |
| [2.1.3](#2.1.3) |8 marca 2019 |--- |
| [2.1.2](#2.1.2) |28 stycznia 2019 |--- |
| [pkt](#2.1.1) |5 grudnia 2018 |--- |
| [2.1.0](#2.1.0) |4 grudnia 2018 r. |--- |
| [2.0.5](#2.0.5) |7 listopada 2018 |--- |
| [2.0.4](#2.0.4) |30 października 2018 |--- |
| [2.0.3](#2.0.3) |30 października 2018 |--- |
| [2.0.2](#2.0.2) |10 października 2018 |--- |
| [2.0.1](#2.0.1) |25 września 2018 r. |--- |
| [2.0.0](#2.0.0) |Wrzesień 24, 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 sierpnia 2018 |--- |
| [1.14.4](#1.14.4) |03, 2018 |30 sierpnia 2020 |
| [1.14.3](#1.14.3) |03, 2018 |30 sierpnia 2020 |
| [1.14.2](#1.14.2) |21 grudnia 2017 |30 sierpnia 2020 |
| [1.14.1](#1.14.1) |10 listopada 2017 |30 sierpnia 2020 |
| [1.14.0](#1.14.0) |9 listopada, 2017 |30 sierpnia 2020 |
| [1.13.0](#1.13.0) |11 października 2017 |30 sierpnia 2020 |
| [1.12.2](#1.12.2) |10 sierpnia 2017 |30 sierpnia 2020 |
| [1.12.1](#1.12.1) |10 sierpnia 2017 |30 sierpnia 2020 |
| [1.12.0](#1.12.0) |10 maja 2017 |30 sierpnia 2020 |
| [1.11.0](#1.11.0) |16 marca 2017 |30 sierpnia 2020 |
| [1.10.2](#1.10.2) |27 stycznia 2017 |30 sierpnia 2020 |
| [1.10.1](#1.10.1) |22 grudnia, 2016 |30 sierpnia 2020 |
| [1.10.0](#1.10.0) |03, 2016 |30 sierpnia 2020 |
| [1.9.0](#1.9.0) |07 lipca 2016 |30 sierpnia 2020 |
| [1.8.0](#1.8.0) |14 czerwca 2016 |30 sierpnia 2020 |
| [1.7.0](#1.7.0) |26 kwietnia 2016 |30 sierpnia 2020 |
| [1.6.0](#1.6.0) |29 marca 2016 |30 sierpnia 2020 |
| [1.5.6](#1.5.6) |08, 2016 |30 sierpnia 2020 |
| [1.5.5](#1.5.5) |02 lutego, 2016 |30 sierpnia 2020 |
| [1.5.4](#1.5.4) |01 lutego, 2016 |30 sierpnia 2020 |
| [1.5.2](#1.5.2) |26 stycznia 2016 |30 sierpnia 2020 |
| [1.5.2](#1.5.2) |22 stycznia 2016 |30 sierpnia 2020 |
| [1.5.1](#1.5.1) |4 stycznia 2016 |30 sierpnia 2020 |
| [1.5.0](#1.5.0) |31 grudnia 2015 |30 sierpnia 2020 |
| [1.4.0](#1.4.0) |06, 2015 |30 sierpnia 2020 |
| [1.3.0](#1.3.0) |06, 2015 |30 sierpnia 2020 |
| [ppkt](#1.2.2) |10 września 2015 |30 sierpnia 2020 |
| [1.2.1](#1.2.1) |15 sierpnia 2015 |30 sierpnia 2020 |
| [1.2.0](#1.2.0) |05 sierpnia, 2015 |30 sierpnia 2020 |
| [1.1.0](#1.1.0) |09 lipca 2015 |30 sierpnia 2020 |
| [1.0.3](#1.0.3) |04, 2015 |30 sierpnia 2020 |
| [1.0.2](#1.0.2) |23 maja 2015 |30 sierpnia 2020 |
| [1.0.1](#1.0.1) |15 maja 2015 |30 sierpnia 2020 |
| [1.0.0](#1.0.0) |08 kwietnia, 2015 |30 sierpnia 2020 |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).