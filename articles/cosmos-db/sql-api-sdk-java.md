---
title: 'Azure Cosmos DB: interfejs API języka Java SQL, zestaw SDK & zasobów'
description: Dowiedz się wszystkiego o interfejsie API i zestawie SDK języka Java SQL, w tym o datach wydaniach, datach wycofania i zmianach wprowadzonych między poszczególnymi wersjami zestawu SDK języka Java Azure Cosmos DB SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6644f495f28fb76503948c18354a5af0fcf832e5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364757"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK dla interfejsu API SQL: informacje o wersji i zasoby
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

Jest to oryginalny zestaw SDK Java Azure Cosmos DB Sync w wersji 2 dla interfejsu API SQL, który obsługuje operacje synchroniczne.

> [!IMPORTANT]  
> To nie *jest najnowszy* zestaw JAVA SDK dla Azure Cosmos DB! Rozważ użycie [Azure Cosmos DB Java SDK w wersji 4](sql-api-sdk-java-v4.md) dla projektu. Aby przeprowadzić uaktualnienie, postępuj zgodnie z instrukcjami w przewodniku Migrate to Azure Cosmos DB Java SDK v4 (Migracja do zestawu SDK języka [Java w wersji 4)](migrate-java-v4-sdk.md) oraz w przewodniku [Reactor vs RxJava (Biblioteki Reactor i RxJava).](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) 
>

| | Linki |
|---|---|
|**Pobierz zestaw SDK**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Dokumentacja interfejsu API**|[Dokumentacja referencyjna interfejsu API języka Java](/java/api/com.microsoft.azure.documentdb)|
|**Współtwomentuj zestaw SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Wprowadzenie**|[Wprowadzenie do zestawu Java SDK](./create-sql-api-java.md)|
|**Samouczek dotyczący aplikacji internetowej**|[Tworzenie aplikacji internetowych za pomocą Azure Cosmos DB](sql-api-java-application.md)|
|**Minimalne obsługiwane środowisko uruchomieniowe**|[Zestaw Java Development Kit (JDK) 7+](/java/azure/jdk/)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="261"></a><a name="2.6.1"></a>2.6.1
* Usunięto usterkę w obsłudze zapytania za pośrednictwem międzyplatkwii usług.

### <a name="260"></a><a name="2.6.0"></a>2.6.0
* Dodano obsługę wykonywania zapytań w kanale zmian z punktu w czasie.

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* Rozwiązuje problem z podstawową pamięcią podręczną partycji w zapytaniu documentCollection.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* Dodano obsługę konfiguracji niestandardowej ponawiania próby 449.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* Rozwiązuje problem z limitem czasu puli połączeń.
* Naprawiono odświeżanie tokenu uwierzytelniania podczas ponownych prób wewnętrznych.

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* Zaktualizowano prawidłowy tag zasad repliki po stronie klienta w bazie danychKonto i wyczyszto konfigurację konta z pamięci podręcznej.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* Unikanie ponawiania w przypadku błędu nieprawidłowego zakresu kluczy partycji, jeśli użytkownik poda wartość pkRangeId.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* Odświeżanie pamięci podręcznej zoptymalizowanego zakresu kluczy partycji.
* Naprawiono scenariusz, w którym zestaw SDK nie dzieli wskazówki podziału partycji z serwera i powoduje odświeżenie pamięci podręcznych routingu po stronie klienta.

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* Zoptymalizowane odświeżanie pamięci podręcznej kolekcji.

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* Dodano obsługę pobierania komunikatu wyjątku wewnętrznego z ciągu diagnostycznego żądania.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* Wprowadzono interfejs API wersji dla partitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* Dodano obsługę oddzielnego limitu czasu dla trybu bezpośredniego.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* Korzystanie z komunikatu o błędzie o wartości null z usługi i tworzenie wyjątku klienta dokumentu.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Ulepszenie połączenia gniazda, dodając wartość domyślną SoKeepAlive true.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* Dodano obsługę ciągów diagnostycznych żądań.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Usunięto usterkę w kluczu PartitionKey dla skrótu w wersji 2.

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Dodano obsługę indeksów złożonych.
* Usunięto usterkę w globalnym menedżerze punktów końcowych, która wymuszała odświeżanie.
* Usunięto usterkę w przypadku operacji upsert z warunkami wstępnymi w trybie bezpośrednim.

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Usunięto usterkę w pamięci podręcznej adresów bramy.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Dodano obsługę zapisu w wielu regionach dla trybu bezpośredniego.
* Dodano obsługę wyjątków IOException zgłaszanych jako wyjątki ServiceUnavailable z serwera proxy.
* Usunięto usterkę w zasadach ponawiania odnajdywania punktów końcowych.
* Usunięto usterkę, która zapewniała, że wyjątki wskaźnika o wartości null nie są zgłaszane w klasy BaseDatabaseAccountConfigurationProvider.
* Usunięto usterkę, która zapewniała, że program QueryIterator nie zwraca wartości null.
* Usunięto usterkę w celu zapewnienia, że klucz PartitionKey jest dozwolony

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Dodano obsługę zapisu w wielu regionach dla trybu bramy.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* Usunięto usterkę w zakresach kluczy partycji odczytu dla zapytania.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* Usunięto usterkę podczas ustawiania rozmiaru nagłówka tokenu kontynuacji w trybie DirectHttps.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* Dodano obsługę trybu fail over przesyłania strumieniowego.
* Dodano obsługę metadanych niestandardowych.
* Ulepszona logika obsługi sesji.
* Usunięto usterkę w pamięci podręcznej zakresu kluczy partycji.
* Usunięto usterkę serwera NPE w trybie bezpośrednim.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* Dodano obsługę unikatowego indeksu.
* Dodano obsługę ograniczania rozmiaru tokenu kontynuacji w opcjach kanału informacyjnego.
* Usunięto usterkę w serializacji JSON (znacznik czasu).
* Usunięto usterkę w serializacji JSON (wyli).
* Zależność od com.fasterxml.core:opis-pliku databind uaktualniono do wersji 2.9.5.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* Ulepszona pula połączeń dla trybu bezpośredniego.
* Ulepszono pobieranie z wyprzedzeniem w przypadku zapytań między partycjami nieu orderby.
* Ulepszone generowanie UUID.
* Ulepszona logika spójności sesji.
* Dodano obsługę wielupolygonów.
* Dodano obsługę statystyk zakresu kluczy partycji dla kolekcji.
* Usunięto usterkę w przypadku obsługi wielu regionów.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* Zwiększona wydajność serializacji JSON.
* Ta wersja zestawu SDK wymaga najnowszej wersji [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator).

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Zmiany wewnętrzne dla bibliotek znajomych firmy Microsoft.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Rozwiązano problem z odczytywaniem zakresów kluczy pojedynczej partycji.
* Rozwiązano problem podczas analizowania resourceID, który wpływa na bazę danych o krótkich nazwach.
* Rozwiązano problem z kodowaniem klucza partycji.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Poprawki błędów krytycznych dotyczące przetwarzania żądań podczas podziałów partycji.
* Rozwiązano problem z poziomami spójności Strong i BoundedStaleness.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Dodano obsługę nowego poziomu spójności o nazwie ConsistentPrefix.
* Usunięto usterkę podczas odczytywania kolekcji w trybie sesji.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Włączono obsługę kolekcji partycjonowanych z 2500 RU/s i skalowanie w przyrostach co 100 RU/s.
* Usunięto usterkę w zestawie natywnym, która w niektórych zapytaniach może powodować wyjątek NullRef.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* Usunięto usterkę w konfiguracji aparatu zapytań, która może powodować wyjątki dla zapytań w trybie bramy.
* Usunięto kilka usterek w kontenerze sesji, które mogą powodować wyjątek "Nie znaleziono zasobu właściciela" dla żądań natychmiast po utworzeniu kolekcji.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i AVG). Zobacz [Obsługa agregacji](sql-query-aggregate-functions.md).
* Dodano obsługę zestawienia zmian.
* Dodano obsługę informacji o limitach przydziału kolekcji za pośrednictwem polecenia RequestOptions.setPopulateQuotaInfo.
* Dodano obsługę rejestrowania skryptów procedury składowanej za pośrednictwem funkcji RequestOptions.setScriptLoggingEnabled.
* Usunięto usterkę, która powodować, że zapytanie w trybie DirectHttps może przestać odpowiadać w przypadku napotkania błędów ograniczania przepustowości.
* Usunięto usterkę w trybie spójności sesji.
* Usunięto usterkę, która może powodować błąd NullReferenceException w httpContext, gdy szybkość żądań jest wysoka.
* Zwiększona wydajność trybu DirectHttps.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* Dodano prostą obsługę serwera proxy opartego na wystąpieniu klienta z interfejsem API ConnectionPolicy.setProxy().
* Dodano interfejs API DocumentClient.close(), aby prawidłowo zamknąć wystąpienie klasy DocumentClient.
* Zwiększona wydajność zapytań w trybie łączności bezpośredniej przez wyprowadzenie planu zapytania z zestawu natywnego zamiast bramy.
* Ustaw FAIL_ON_UNKNOWN_PROPERTIES = false, aby użytkownicy nie musieli definiować JsonIgnoreProperties w swoich obiektach POJO.
* Zrefactored logging to use SLF4J (Refaktoryzacja rejestrowania w celu używania SLF4J).
* Usunięto kilka innych usterek w czytniku spójności.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* Usunięto usterkę w zarządzaniu połączeniami, która uniemożliwiała przecieki połączenia w trybie łączności bezpośredniej.
* Usunięto usterkę w zapytaniu TOP, która może zgłaszać wyjątek NullReference.
* Zwiększona wydajność dzięki zmniejszeniu liczby wywołań sieciowych dla wewnętrznych pamięci podręcznych.
* W celu lepszego rozwiązywania problemów dodano kod stanu, activityID i request URI w dokumencie DocumentClientException.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* Rozwiązano problem z zarządzaniem połączeniami w celu stabilności.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* Dodano obsługę poziomu spójności BoundedStaleness.
* Dodano obsługę bezpośredniej łączności dla operacji CRUD dla kolekcji podzielonych na partycje.
* Usunięto usterkę podczas wykonywania zapytań w bazie danych przy użyciu języka SQL.
* Usunięto usterkę w pamięci podręcznej sesji, która powodować nieprawidłowe ustawienie tokenu sesji.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Dodano obsługę zapytań równoległych między partycjami.
* Dodano obsługę zapytań TOP/ORDER BY dla kolekcji podzielonych na partycje.
* Dodano obsługę silnej spójności.
* Dodano obsługę żądań opartych na nazwach w przypadku korzystania z łączności bezpośredniej.
* Naprawiono, aby activityId pozostał spójny we wszystkich ponownych próbach żądania.
* Usunięto usterkę związaną z pamięcią podręczną sesji podczas ponownego tworzenia kolekcji o tej samej nazwie.
* Dodano typy danych Polygon i LineString podczas określania zasad indeksowania kolekcji dla zapytań przestrzennych ogrodzenia geograficznego.
* Rozwiązano problemy z programem Java Doc for Java 1.8.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* Usunięto usterkę w elementach PartitionKeyDefinitionMap w celu buforowania kolekcji z jedną partycją i nie pobierania dodatkowych żądań klucza partycji.
* Usunięto usterkę, która nie ponawiała próby po podano nieprawidłową wartość klucza partycji.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Dodano obsługę kont bazy danych w wielu regionach.
* Dodano obsługę automatycznego ponawiania w przypadku żądań z ograniczeniami z opcjami dostosowywania maksymalnej liczby ponownych prób i maksymalnego czasu oczekiwania na ponowną próbę.  Zobacz retryOptions i ConnectionPolicy.getRetryOptions().
* Przestarzały niestandardowy kod partycjonowania oparty na funkcji IPartitionResolver. Użyj kolekcji partycjonowanych w celu zwiększenia magazynu i przepływności.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* Dodano obsługę zasad ponawiania dla ograniczania szybkości.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Dodano obsługę czasu wygaśnięcia (TTL) dla dokumentów.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Zaimplementowano [kolekcje partycjonowane](partitioning-overview.md) [i zdefiniowane przez użytkownika poziomy wydajności.](performance-levels.md)

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Usunięto usterkę w funkcji HashPartitionResolver w celu generowania wartości skrótu w little-endian w celu zachowania spójności z innymi zestawami SDK.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Dodaj funkcje rozpoznawania & partycji zakresu skrótów, aby pomóc w fragmentowania aplikacji na wielu partycjach.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Zaim implementuj upsert. Dodano nowe metody upsertXXX do obsługi funkcji Upsert.
* Implementowanie routingu opartego na identyfikatorach. Brak zmian publicznego interfejsu API, wszystkie zmiany wewnętrzne.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Pominięto wydanie, aby numer wersji był wyrównany z innymi zestawami SDK

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Obsługuje indeks geoprzestrzenny
* Weryfikuje właściwość ID dla wszystkich zasobów. Identyfikatory zasobów nie mogą zawierać znaków ?, /, #, \, ani kończyć się spacją.
* Dodaje nowy nagłówek "postęp przekształcania indeksu" do klasy ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementuje zasady indeksowania w wersji 2

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* Zestaw SDK ogólnej dostępności

## <a name="release-and-retirement-dates"></a>Daty wydania i wycofania

Firma Microsoft udostępni powiadomienie co najmniej **12 miesięcy** przed wycofaniem zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji. Nowe funkcje i optymalizacje są dodawane tylko do bieżącego zestawu SDK, dlatego zaleca się, aby zawsze uaktualniać do najnowszej wersji zestawu SDK tak wcześnie, jak to możliwe.

> [!WARNING]
> Po 30 maja 2020 r. usługa Azure Cosmos DB nie będzie już zawierać poprawek błędów, dodawać nowych funkcji i zapewniać obsługę wersji 1.x zestawu JAVA SDK dla interfejsu API SQL Azure Cosmos DB. Jeśli nie chcesz przeprowadzać uaktualnienia, żądania wysyłane z wersji 1.x zestawu SDK będą nadal obsługiwane przez usługę Azure Cosmos DB.
>
> Po 29 lutego 2016 r. usługa Azure Cosmos DB nie będzie już świadczyć poprawek błędów, dodawać nowych funkcji i zapewniać obsługę wersji 0.x zestawu AZURE COSMOS DB Java SDK dla interfejsu API SQL. Jeśli nie chcesz uaktualniać, żądania wysyłane z wersji 0.x zestawu SDK będą nadal obsługiwane przez Azure Cosmos DB usługi.


| Wersja | Data wydania | Data wycofania |
| --- | --- | --- |
| [2.6.1](#2.6.1) |Dec 17, 2020 |--- |
| [2.6.0](#2.6.0) |16 lipca 2020 r. |--- |
| [2.5.1](#2.5.1) |3 czerwca 2020 r. |--- |
| [2.5.0](#2.5.0) |12 maj 2020 r. |--- |
| [2.4.7](#2.4.7) |20 lutego 2020 r. |--- |
| [2.4.6](#2.4.6) |24 stycznia 2020 r. |--- |
| [2.4.5](#2.4.5) |10 listopada 2019 r. |--- |
| [2.4.4](#2.4.4) |24 października 2019 r. |--- |
| [2.4.2](#2.4.2) |26 września 2019 r. |--- |
| [2.4.1](#2.4.1) |18 lipca 2019 r. |--- |
| [2.4.0](#2.4.0) |4 maja 2019 r. |--- |
| [2.3.0](#2.3.0) |24 kw. 2019 r. |--- |
| [2.2.3](#2.2.3) |16 kw. 2019 r. |--- |
| [2.2.2](#2.2.2) |5 kw. 2019 r. |--- |
| [2.2.0](#2.2.0) |27 marca 2019 r. |--- |
| [2.1.3](#2.1.3) |13 marca 2019 r. |--- |
| [2.1.2](#2.1.2) |9 marca 2019 r. |--- |
| [2.1.1](#2.1.1) |Dec 13, 2018 |--- |
| [2.1.0](#2.1.0) |20 listopada 2018 r. |--- |
| [2.0.0](#2.0.0) |21 września 2018 r. |--- |
| [1.16.4](#1.16.4) |10 września 2018 r. |30 maja 2020 r. |
| [1.16.3](#1.16.3) |9 września 2018 r. |30 maja 2020 r. |
| [1.16.2](#1.16.2) |29 czerwca 2018 r. |30 maja 2020 r. |
| [1.16.1](#1.16.1) |16 maja 2018 r. |30 maja 2020 r. |
| [1.16.0](#1.16.0) |15 marca 2018 r. |30 maja 2020 r. |
| [1.15.0](#1.15.0) |14 listopada 2017 r. |30 maja 2020 r. |
| [1.14.0](#1.14.0) |28 października 2017 r. |30 maja 2020 r. |
| [1.13.0](#1.13.0) |25 sierpnia 2017 r. |30 maja 2020 r. |
| [1.12.0](#1.12.0) |11 lipca 2017 r. |30 maja 2020 r. |
| [1.11.0](#1.11.0) |10 maja 2017 |30 maja 2020 r. |
| [1.10.0](#1.10.0) |11 marca 2017 r. |30 maja 2020 r. |
| [1.9.6](#1.9.6) |21 lutego 2017 r. |30 maja 2020 r. |
| [1.9.5](#1.9.5) |31 stycznia 2017 r. |30 maja 2020 r. |
| [1.9.4](#1.9.4) |24 listopada 2016 r. |30 maja 2020 r. |
| [1.9.3](#1.9.3) |30 października 2016 r. |30 maja 2020 r. |
| [1.9.2](#1.9.2) |28 października 2016 r. |30 maja 2020 r. |
| [1.9.1](#1.9.1) |26 października 2016 r. |30 maja 2020 r. |
| [1.9.0](#1.9.0) |3 października 2016 r. |30 maja 2020 r. |
| [1.8.1](#1.8.1) |30 czerwca 2016 r. |30 maja 2020 r. |
| [1.8.0](#1.8.0) |14 czerwca 2016 r. |30 maja 2020 r. |
| [1.7.1](#1.7.1) |30 kwietnia 2016 r. |30 maja 2020 r. |
| [1.7.0](#1.7.0) |27 kwietnia 2016 r. |30 maja 2020 r. |
| [1.6.0](#1.6.0) |29 marca 2016 r. |30 maja 2020 r. |
| [1.5.1](#1.5.1) |31 grudnia 2015 r. |30 maja 2020 r. |
| [1.5.0](#1.5.0) |4 grudnia 2015 r. |30 maja 2020 r. |
| [1.4.0](#1.4.0) |5 października 2015 r. |30 maja 2020 r. |
| [1.3.0](#1.3.0) |5 października 2015 r. |30 maja 2020 r. |
| [1.2.0](#1.2.0) |5 sierpnia 2015 r. |30 maja 2020 r. |
| [1.1.0](#1.1.0) |9 lipca 2015 r. |30 maja 2020 r. |
| 1.0.1 |12 maja 2015 r. |30 maja 2020 r. |
| [1.0.0](#1.0.0) |7 kwietnia 2015 r. |30 maja 2020 r. |
| 0.9.5 — wstępnie |9 marca 2015 r. |29 lutego 2016 r. |
| 0.9.4 — wstępnie |17 lutego 2015 r. |29 lutego 2016 r. |
| 0.9.3 — prelease |13 stycznia 2015 r. |29 lutego 2016 r. |
| 0.9.2 — prelease |19 grudnia 2014 r. |29 lutego 2016 r. |
| 0.9.1 — prelease |19 grudnia 2014 r. |29 lutego 2016 r. |
| 0.9.0 — prelease |10 grudnia 2014 r. |29 lutego 2016 r. |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
