---
title: 'Azure Cosmos DB: interfejs API Java języka SQL, zasoby & zestawu SDK'
description: Dowiedz się więcej o interfejsie API Java i zestawie SDK programu SQL, w tym o datach wydania, datach wycofania i zmianach między poszczególnymi wersjami Azure Cosmos DB SQL Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 0b48b8d954b132caab96e5978c90687899ea04c2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549229"
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
> * [Łącznik platformy Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Moduł wykonywania zbiorczego — .NET 2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonywania zbiorczego — Java](sql-api-sdk-bulk-executor-java.md)

Jest to oryginalny Azure Cosmos DB synchronizacji zestawu Java SDK V2 dla interfejsu API SQL, który obsługuje operacje synchroniczne.

> [!IMPORTANT]  
> To *nie* jest najnowszy zestaw SDK języka Java dla Azure Cosmos DB! Rozważ użycie [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) dla projektu. Aby przeprowadzić uaktualnienie, postępuj zgodnie z instrukcjami zawartymi w przewodniku [Migrowanie do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) i w podręczniku [reaktora vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) . 
>

| |  |
|---|---|
|**Pobieranie zestawu SDK**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API języka Java](/java/api/com.microsoft.azure.documentdb)|
|**Współtworzenie zestawu SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Wprowadzenie**|[Wprowadzenie do zestawu Java SDK](./create-sql-api-java.md)|
|**Samouczek aplikacji sieci Web**|[Tworzenie aplikacji sieci Web za pomocą Azure Cosmos DB](sql-api-java-application.md)|
|**Minimalne obsługiwane środowisko uruchomieniowe**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* Rozwiązuje problem z pamięcią podręczną partycji podstawowej w zapytaniu documentcollection.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* Dodano obsługę niestandardowej konfiguracji ponowień 449.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* Rozwiązuje problem z limitem czasu puli połączeń.
* Naprawia odświeżanie tokenu uwierzytelniania podczas wewnętrznych ponownych prób.

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* Zaktualizowano prawidłowy tag zasad repliki po stronie klienta na databaseAccount i databaseAccounte odczyty konfiguracji z pamięci podręcznej.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* Unikaj ponawiania próby przy nieprawidłowym błędzie zakresu kluczy partycji, jeśli użytkownik poda pkRangeId.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* Odświeżone pamięci podręcznej zakresów kluczy partycji.
* Poprawka w scenariuszu, w którym zestaw SDK nie obsługuje wskazówki podziału partycji z serwera i powoduje nieprawidłowe Odświeżanie pamięci podręcznych routingu po stronie klienta.

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* Odświeżone Odświeżanie pamięci podręcznej kolekcji.

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* Dodano obsługę pobierania komunikatu o wyjątku wewnętrznym z ciągu diagnostyki żądania.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* Wprowadzono wersję interfejsu API w systemie PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* Dodano obsługę oddzielnego limitu czasu dla trybu bezpośredniego.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* Zużywanie pustego komunikatu o błędzie z usługi i Tworzenie wyjątku klienta dokumentu.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Udoskonalenie połączenia gniazda, Dodawanie SoKeepAlive domyślnego true.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* Dodano obsługę ciągu diagnostyki żądania.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Naprawiono usterkę w PartitionKey dla skrótu v2.

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Dodano obsługę indeksów złożonych.
* Naprawiono usterkę w programie Global Endpoint Manager, aby wymusić odświeżanie.
* Stała usterka dla upserts z warunkiem wstępnym w trybie bezpośrednim.

### <a name="211"></a><a name="2.1.1"></a>pkt
* Naprawiono usterkę w pamięci podręcznej adresów bramy.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Dodano obsługę zapisu wieloregionowego dla trybu bezpośredniego.
* Dodano obsługę obsługi IOExceptions zgłoszonych jako wyjątki serviceavailable z serwera proxy.
* Naprawiono usterkę w zasadach ponawiania odnajdywania punktów końcowych.
* Rozwiązano błąd, aby upewnić się, że wyjątki wskaźnika o wartości null nie są zgłaszane w BaseDatabaseAccountConfigurationProvider.
* Rozwiązano błąd, aby upewnić się, że QueryIterator nie zwraca wartości null.
* Rozwiązano błąd w celu zapewnienia, że jest dozwolony duży PartitionKey

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Dodano obsługę zapisu wieloregionowego dla trybu bramy.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* Naprawiono usterkę w zakresach kluczy partycji odczytu dla zapytania.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* Rozwiązano błąd podczas ustawiania rozmiaru nagłówka tokenu kontynuacji w trybie DirectHttps.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* Dodano obsługę przesyłania strumieniowego w trybie failover.
* Dodano obsługę metadanych niestandardowych.
* Ulepszona logika obsługi sesji.
* Naprawiono usterkę w pamięci podręcznej zakresów kluczy partycji.
* Naprawiono usterkę aparatu NPE w trybie bezpośrednim.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* Dodano obsługę unikatowego indeksu.
* Dodano obsługę ograniczania rozmiaru tokenu kontynuacji w opcjach źródła danych.
* Naprawiono usterkę w serializacji JSON (Sygnatura czasowa).
* Naprawiono usterkę w serializacji JSON (enum).
* Zależność od modelu com. fasterxml. Jacksona. Core: Jacksona-DataBind uaktualnionego do 2.9.5.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* Ulepszono obsługę puli połączeń dla trybu bezpośredniego.
* Ulepszone ulepszenie pobierania z wyprzedzeniem dla zapytania obejmującego wiele partycji.
* Ulepszone generowanie identyfikatora UUID.
* Ulepszona logika spójności sesji.
* Dodano obsługę MultiPolygon.
* Dodano obsługę statystyk zakresu kluczy partycji dla kolekcji.
* Rozwiązano usterkę w obsłudze wieloregionowej.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* Ulepszona wydajność serializacji JSON.
* Ta wersja zestawu SDK wymaga najnowszej wersji [emulatora Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Wewnętrzne zmiany w bibliotekach zaprzyjaźnionych firmy Microsoft.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Rozwiązano problem podczas odczytywania zakresów kluczy pojedynczej partycji.
* Rozwiązano problem związany z analizą ResourceID, który ma wpływ na bazę danych z krótkimi nazwami.
* Rozwiązano problem spowodowany przez kodowanie klucza partycji.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Krytyczne poprawki błędów do przetwarzania żądań podczas dzielenia partycji.
* Rozwiązano problem z poziomami spójności silny i BoundedStaleness.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Dodano obsługę nowego poziomu spójności o nazwie ConsistentPrefix.
* Rozwiązano błąd podczas odczytywania kolekcji w trybie sesji.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Włączono obsługę partycjonowanych kolekcji z niską ilością 2 500 RU/s i skalą przyrostową 100 RU/s.
* Naprawiono usterkę w zestawie natywnym, co może spowodować wyjątek NullRef w niektórych zapytaniach.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* Naprawiono usterkę w konfiguracji aparatu zapytań, która może spowodować wyjątki dla zapytań w trybie bramy.
* Rozwiązano kilka usterek w kontenerze sesji, które mogą spowodować wyjątek "nie znaleziono zasobu właściciela" dla żądań natychmiast po utworzeniu kolekcji.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i AVG). Zobacz [Obsługa agregacji](sql-query-aggregate-functions.md).
* Dodano obsługę źródła zmian.
* Dodano obsługę informacji o limicie przydziału kolekcji za pomocą RequestOptions. setPopulateQuotaInfo.
* Dodano obsługę rejestrowania skryptów procedury składowanej za pomocą RequestOptions. setScriptLoggingEnabled.
* Naprawiono usterkę polegającą na tym, że zapytanie w trybie DirectHttps może przestać odpowiadać, gdy wystąpią błędy ograniczania.
* Naprawiono usterkę w trybie spójności sesji.
* Rozwiązano błąd, który może spowodować NullReferenceException w elemencie HttpContext, gdy liczba żądań jest wysoka.
* Ulepszona wydajność trybu DirectHttps.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* Dodano obsługę prostego serwera proxy opartego na wystąpieniu klienta z interfejsem API ConnectionPolicy. SetProxy ().
* Dodano interfejs API DocumentClient. Close () do prawidłowego zamknięcia wystąpienia DocumentClient.
* Ulepszona wydajność zapytań w trybie bezpośredniej łączności przez wytworzenie planu zapytania z zestawu natywnego zamiast bramy.
* Ustaw wartość FAIL_ON_UNKNOWN_PROPERTIES = false, aby użytkownicy nie musieli definiować JsonIgnoreProperties w POJO.
* Refaktoryzacja rejestrowania do użycia SLF4J.
* Rozwiązano kilka innych usterek w czytniku spójności.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* Naprawiono usterkę w zarządzaniu połączeniami, aby zapobiec przeciekom połączeń w trybie bezpośredniej łączności.
* Rozwiązano usterkę w zapytaniu TOP, w której może zgłosić wyjątek NullReference.
* Zwiększona wydajność dzięki zmniejszeniu liczby wywołań sieciowych dla wewnętrznych pamięci podręcznych.
* Dodano kod stanu, ActivityID i identyfikator URI żądania w programie DocumentClientException w celu lepszego rozwiązywania problemów.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* Rozwiązano problem związany z zarządzaniem połączeniem w celu zapewnienia stabilności.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* Dodano obsługę poziomu spójności BoundedStaleness.
* Dodano obsługę bezpośredniej łączności dla operacji CRUD dla kolekcji partycjonowanych.
* Rozwiązano błąd podczas wykonywania zapytania dotyczącego bazy danych SQL.
* Naprawiono usterkę w pamięci podręcznej sesji, w której token sesji może być ustawiony nieprawidłowo.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Dodano obsługę zapytań równoległych między partycjami.
* Dodano obsługę funkcji TOP/ORDER przez zapytania dotyczące kolekcji partycjonowanych.
* Dodano obsługę silnej spójności.
* Dodano obsługę żądań nazw opartych na nazwach podczas korzystania z bezpośredniej łączności.
* Naprawiono, aby ActivityId zachować spójność wszystkich ponownych prób żądania.
* Naprawiono usterkę związaną z pamięcią podręczną sesji podczas ponownego tworzenia kolekcji o tej samej nazwie.
* Dodano wielokąta i LineString typy danych podczas określania zasad indeksowania kolekcji dla zapytań przestrzennych geograficznych.
* Rozwiązano problemy z dokumentem Java w języku Java 1,8.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* Naprawiono usterkę w PartitionKeyDefinitionMap, która umożliwia buforowanie pojedynczych kolekcji partycji i nie tworzy dodatkowych żądań dotyczących kluczy partycji pobierania.
* Rozwiązano błąd, aby nie ponawiać próby w przypadku podanej nieprawidłowej wartości klucza partycji.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Dodano obsługę wieloregionowych kont baz danych.
* Dodano obsługę automatycznego ponawiania prób w żądaniach ograniczających z opcjami, aby dostosować maksymalną liczbę ponownych prób i maksymalny czas oczekiwania na ponowienie próby.  Zobacz RetryOptions i ConnectionPolicy. getRetryOptions ().
* Przestarzały kod partycjonowania niestandardowego oparty na IPartitionResolver. Użyj partycjonowanych kolekcji w celu zapewnienia większego magazynu i przepływności.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* Dodano obsługę zasad ponawiania dla ograniczania szybkości.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Dodano obsługę czasu wygaśnięcia (TTL) dla dokumentów.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Wdrożone [kolekcje partycjonowane](partitioning-overview.md) i [poziomy wydajności zdefiniowane przez użytkownika](performance-levels.md).

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Naprawiono usterkę w HashPartitionResolver, aby generować wartości skrótu w postaci little-endian, aby były spójne z innymi zestawami SDK.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Dodaj & rozpoznawania partycji zakresu skrótów, aby pomóc w fragmentowania aplikacji na wielu partycjach.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Zaimplementuj upsert. Nowe metody upsertXXX dodane do obsługi funkcji upsert.
* Implementowanie routingu opartego na IDENTYFIKATORach. Brak publicznych zmian interfejsu API, wszystkie zmiany wewnętrzne.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Wersja pominięta, aby wprowadzić numer wersji w wyrównaniu z innymi zestawami SDK

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Obsługuje indeks geoprzestrzenny
* Weryfikuje Właściwość identyfikatora dla wszystkich zasobów. Identyfikatory dla zasobów nie mogą zawierać znaków?,/, #, \, ani kończyć się spacją.
* Dodaje nowy nagłówek "postęp transformacji indeksu" do ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementuje zasady indeksowania v2

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* Zestaw SDK ogólnej dostępności

## <a name="release-and-retirement-dates"></a>Daty wydania i wycofania

Firma Microsoft udostępni powiadomienie co najmniej **12 miesięcy** przed wycofaniem zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji. Nowe funkcje i funkcje i optymalizacje są dodawane tylko do bieżącego zestawu SDK, dlatego zaleca się, aby zawsze uaktualnić do najnowszej wersji zestawu SDK tak szybko, jak to możliwe.

> [!WARNING]
> Po 30 maju 2020 Azure Cosmos DB nie będą już naprawiać błędów, dodawać nowych funkcji i zapewniać pomoc techniczną do wersji 1. x Azure Cosmos DB Java SDK dla interfejsu API SQL. Jeśli nie chcesz przeprowadzać uaktualnienia, żądania wysyłane z wersji 1.x zestawu SDK będą nadal obsługiwane przez usługę Azure Cosmos DB.
>
> Po 29 lutego 2016 Azure Cosmos DB nie będą już naprawiać błędów, dodawać nowych funkcji i zapewniać pomoc techniczną do wersji 0. x Azure Cosmos DB Java SDK dla interfejsu API SQL. Jeśli wolisz nie aktualizować, żądania wysyłane z wersji 0. x zestawu SDK będą nadal obsługiwane przez usługę Azure Cosmos DB.


| Wersja | Data wydania | Data wycofania |
| --- | --- | --- |
| [2.5.1](#2.5.1) |03, 2020 |--- |
| [2.5.0](#2.5.0) |12 maj 2020 r. |--- |
| [2.4.7](#2.4.7) |20 lutego 2020 |--- |
| [2.4.6](#2.4.6) |24 stycznia 2020 |--- |
| [2.4.5](#2.4.5) |Lis 10, 2019 |--- |
| [2.4.4](#2.4.4) |24 października 2019 |--- |
| [2.4.2](#2.4.2) |26 SEP, 2019 |--- |
| [2.4.1](#2.4.1) |18 lipca 2019 |--- |
| [2.4.0](#2.4.0) |04, 2019 |--- |
| [2.3.0](#2.3.0) |24 kwietnia 2019 |--- |
| [2.2.3](#2.2.3) |16 kwietnia 2019 |--- |
| [2.2.2](#2.2.2) |Kwi 05, 2019 |--- |
| [2.2.0](#2.2.0) |Mar 27, 2019 |--- |
| [2.1.3](#2.1.3) |Mar 13, 2019 |--- |
| [2.1.2](#2.1.2) |Mar 09, 2019 |--- |
| [pkt](#2.1.1) |13 grudnia 2018 |--- |
| [2.1.0](#2.1.0) |20 listopada 2018 |--- |
| [2.0.0](#2.0.0) |Września 21, 2018 |--- |
| [1.16.4](#1.16.4) |Września 10, 2018 |30 maja 2020 |
| [1.16.3](#1.16.3) |Września 09, 2018 |30 maja 2020 |
| [1.16.2](#1.16.2) |29 czerwca 2018 |30 maja 2020 |
| [1.16.1](#1.16.1) |16 maja 2018 |30 maja 2020 |
| [1.16.0](#1.16.0) |15 marca 2018 |30 maja 2020 |
| [1.15.0](#1.15.0) |14 listopada 2017 |30 maja 2020 |
| [1.14.0](#1.14.0) |28 października 2017 |30 maja 2020 |
| [1.13.0](#1.13.0) |25 sierpnia 2017 |30 maja 2020 |
| [1.12.0](#1.12.0) |11 lipca 2017 r. |30 maja 2020 |
| [1.11.0](#1.11.0) |10 maja 2017 |30 maja 2020 |
| [1.10.0](#1.10.0) |11 marca 2017 |30 maja 2020 |
| [1.9.6](#1.9.6) |21 lutego 2017 |30 maja 2020 |
| [1.9.5](#1.9.5) |31 stycznia 2017 |30 maja 2020 |
| [1.9.4](#1.9.4) |24 listopada 2016 |30 maja 2020 |
| [1.9.3](#1.9.3) |30 października 2016 |30 maja 2020 |
| [1.9.2](#1.9.2) |28 października 2016 |30 maja 2020 |
| [1.9.1](#1.9.1) |26 października 2016 |30 maja 2020 |
| [1.9.0](#1.9.0) |03, 2016 |30 maja 2020 |
| [1.8.1](#1.8.1) |30 czerwca 2016 |30 maja 2020 |
| [1.8.0](#1.8.0) |14 czerwca 2016 |30 maja 2020 |
| [1.7.1](#1.7.1) |30 kwietnia 2016 |30 maja 2020 |
| [1.7.0](#1.7.0) |27 kwietnia 2016 |30 maja 2020 |
| [1.6.0](#1.6.0) |29 marca 2016 |30 maja 2020 |
| [1.5.1](#1.5.1) |31 grudnia 2015 |30 maja 2020 |
| [1.5.0](#1.5.0) |04 grudnia, 2015 |30 maja 2020 |
| [1.4.0](#1.4.0) |05, 2015 |30 maja 2020 |
| [1.3.0](#1.3.0) |05, 2015 |30 maja 2020 |
| [1.2.0](#1.2.0) |05 sierpnia, 2015 |30 maja 2020 |
| [1.1.0](#1.1.0) |09 lipca 2015 |30 maja 2020 |
| 1.0.1 |12 maja 2015 |30 maja 2020 |
| [1.0.0](#1.0.0) |07 kwietnia 2015 |30 maja 2020 |
| 0.9.5 — przedlease |Mar 09, 2015 |29 lutego 2016 |
| 0.9.4 — przedlease |17 lutego 2015 |29 lutego 2016 |
| 0.9.3 — przedlease |13 stycznia 2015 |29 lutego 2016 |
| 0.9.2 — przedlease |19 grudnia 2014 |29 lutego 2016 |
| od 0.9.1 — przedlease |19 grudnia 2014 |29 lutego 2016 |
| 0.9.0 — przedlease |10 grudnia 2014 |29 lutego 2016 |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).