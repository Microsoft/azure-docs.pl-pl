---
title: 'Azure Cosmos DB: interfejs API języka SQL Python, zasoby & zestawu SDK'
description: Dowiedz się więcej o interfejsie API języka SQL Python i zestawie SDK, w tym o datach wydania, datach wycofania i zmianach między poszczególnymi wersjami Azure Cosmos DB Python SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: ce74a7b02b9c450e07402d96a1aaba2a4b704788
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650721"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK dla interfejsu API SQL: informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Źródło zmian platformy .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Zestaw Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK 2](sql-api-sdk-async-java.md)
> * [Sync Java SDK 2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonawczy zbiorczej — Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Pobierz zestaw SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API języka Python](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Instrukcje dotyczące instalacji zestawu SDK**|[Instrukcje dotyczące instalacji zestawu SDK języka Python](https://github.com/Azure/azure-cosmos-python)|
|**Współtworzenie zestawu SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Rozpoczęcie pracy**|[Wprowadzenie do zestawu SDK języka Python](sql-api-python-application.md)|
|**Bieżąca obsługiwana platforma**|[Python 2,7](https://www.python.org/downloads/) i [Python 3,5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="302"></a><a name="3.0.2"/>3.0.2
* Dodano obsługę elementu DataType MultiPolygon
* Poprawka błędu w zasadach ponawiania próby odczytu sesji
* Poprawka usterki dla nieprawidłowych problemów z uzupełnianiem podczas dekodowania podstawowych 64 ciągów

### <a name="301"></a><a name="3.0.1"/>3.0.1
* Poprawka błędu w LocationCache
* Usterka logiki ponawiania punktu końcowego
* Stała dokumentacja

### <a name="300"></a><a name="3.0.0"/>3.0.0
* Obsługa zapisu w ramach wieloregionu.
* Przestrzeń nazw została zmieniona na Azure. Cosmos.
* Pojęcia dotyczące kolekcji i dokumentów zostały zmienione na kontener i element, document_client zmieniono nazwę na cosmos_client. 

### <a name="233"></a><a name="2.3.3"/>2.3.3
* Dodano obsługę serwera proxy
* Dodano obsługę kanału informacyjnego zmiany odczytu
* Dodano obsługę nagłówków przydziału kolekcji
* Poprawka dla problemu z tokenami dużej sesji
* Poprawka dla interfejsu API ReadMedia
* Poprawka w pamięci podręcznej zakresów kluczy partycji

### <a name="232"></a><a name="2.3.2"/>2.3.2
* Dodano obsługę domyślnych ponownych prób w przypadku problemów z połączeniem.

### <a name="231"></a><a name="2.3.1"/>ppkt
* Zaktualizowana dokumentacja dotycząca referencyjnego Azure Cosmos DB zamiast platformy Azure DocumentDB.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Ta wersja zestawu SDK wymaga najnowszej wersji [emulatora Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Poprawka usterki dla słownika agregacji.
* Poprawka usterki dla przycinania ukośników w łączu zasobów.
* Dodano testy dla kodowania Unicode.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Dodano obsługę nowego poziomu spójności o nazwie ConsistentPrefix.


### <a name="210"></a><a name="2.1.0"/>2.1.0
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i AVG).
* Dodano opcję wyłączania weryfikacji protokołu TLS podczas uruchamiania z emulatorem Cosmos DB.
* Usunięto ograniczenie zależnego modułu żądań, aby dokładnie 2.10.0.
* Obniżona minimalna przepływność na partycjonowanych kolekcjach z 10 100 RU/s do 2500 RU/s.
* Dodano obsługę włączania rejestrowania skryptów podczas wykonywania procedury składowanej.
* Wersja interfejsu API REST dotrzymana do "2017-01-19" w tej wersji.

### <a name="201"></a><a name="2.0.1"/>2.0.1
* Wprowadzono zmiany redakcyjne w komentarzach dokumentacji.

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Dodano obsługę języka Python 3,5.
* Dodano obsługę puli połączeń przy użyciu modułu żądania.
* Dodano obsługę spójności sesji.
* Dodano obsługę zapytań TOP/ORDERBY dla kolekcji partycjonowanych.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Dodano obsługę zasad ponawiania dla żądań z ograniczeniami. (Żądania ograniczające żądania odbierają zbyt duży wyjątek, kod błędu 429). Domyślnie program Azure Cosmos DB ponownych prób w przypadku każdego żądania, gdy zostanie napotkany kod błędu 429, a w nagłówku odpowiedzi zostanie osiągnięty czas retryAfter. Ustalony czas interwału ponowienia próby można teraz ustawić jako część właściwości RetryOptions obiektu ConnectionPolicy, jeśli chcesz zignorować czas retryAfter zwrócony przez serwer między ponownymi próbami. Azure Cosmos DB teraz czeka przez maksymalnie 30 sekund dla każdego żądania, które jest ograniczane (niezależnie od liczby ponownych prób) i zwraca odpowiedź z kodem błędu 429. Ten czas można również zastąpić we właściwości RetryOptions obiektu ConnectionPolicy.
* Cosmos DB teraz zwraca wartość x-MS-dławienia-retry i licznik x-MS-dławienia-retry-Time-MS jako nagłówki odpowiedzi w każdym żądaniu, aby zauważyć licznik ponownych prób ograniczenia i skumulowany czas oczekiwania żądania między ponownymi próbami.
* Usunięto klasę RetryPolicy i odpowiadającą jej właściwość (retry_policy) uwidocznioną na klasie document_client i zamiast tego wprowadzamy klasę RetryOptions, która uwidacznia Właściwość RetryOptions w klasie ConnectionPolicy, która może być używana do przesłonięcia niektórych domyślnych opcji ponowień.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* Dodano obsługę wieloregionowych kont baz danych.

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Dodano obsługę funkcji Time to Live (TTL) dla dokumentów.

### <a name="161"></a><a name="1.6.1"/>1.6.1
* Poprawki błędów powiązane z partycjonowaniem po stronie serwera, aby zezwolić na znaki specjalne w ścieżce klucza partycji.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Wdrożone [kolekcje partycjonowane](partition-data.md) i [poziomy wydajności zdefiniowane przez użytkownika](performance-levels.md). 

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Dodaj & rozpoznawania partycji zakresu skrótów, aby pomóc w fragmentowania aplikacji na wielu partycjach.

### <a name="142"></a><a name="1.4.2"/>1.4.2
* Zaimplementuj upsert. Nowe metody UpsertXXX dodane do obsługi funkcji upsert.
* Implementowanie routingu opartego na IDENTYFIKATORach. Brak publicznych zmian interfejsu API, wszystkie zmiany wewnętrzne.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Obsługuje indeks geoprzestrzenny.
* Weryfikuje Właściwość identyfikatora dla wszystkich zasobów. Identyfikatory dla zasobów nie mogą zawierać znaków?,/, #, \, ani kończyć się spacją.
* Dodaje nowy nagłówek "postęp transformacji indeksu" do ResourceResponse.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implementuje zasady indeksowania w wersji 2.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Obsługuje połączenie z serwerem proxy.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* ZESTAW SDK.

## <a name="release--retirement-dates"></a>Data wycofania &
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** przed WYCOFANIEM zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji.

Nowe funkcje i funkcje i optymalizacje są dodawane tylko do bieżącego zestawu SDK, dlatego zaleca się, aby zawsze uaktualnić do najnowszej wersji zestawu SDK tak szybko, jak to możliwe. 

Wszystkie żądania Cosmos DB korzystania z wycofanego zestawu SDK są odrzucane przez usługę.

> [!WARNING]
> Wszystkie wersje zestawu SDK języka Python dla interfejsu API SQL w wersji starszej niż **1.0.0** zostały wycofane **29 lutego 2016**. 
> 
> 

> [!WARNING]
> Wszystkie wersje 1. x i 2. x zestawu SDK języka Python dla interfejsu API SQL zostaną wycofane **30 sierpnia 2020**. 
> 
> 

<br/>

| Wersja | Data wydania | Data wycofania |
| --- | --- | --- |
| [3.0.2](#3.0.2) |LIS 15, 2018 |--- |
| [3.0.1](#3.0.1) |04, 2018 |--- |
| [2.3.3](#2.3.3) |Września 08, 2018 |30 sierpnia 2020 |
| [2.3.2](#2.3.2) |Maja 08, 2018 |30 sierpnia 2020 |
| [ppkt](#2.3.1) |21 grudnia 2017 |30 sierpnia 2020 |
| [2.3.0](#2.3.0) |10 listopada 2017 |30 sierpnia 2020 |
| [2.2.1](#2.2.1) |29 wrz 2017 |30 sierpnia 2020 |
| [2.2.0](#2.2.0) |10 maja 2017 |30 sierpnia 2020 |
| [2.1.0](#2.1.0) |01 maja, 2017 |30 sierpnia 2020 |
| [2.0.1](#2.0.1) |30 października 2016 |30 sierpnia 2020 |
| [2.0.0](#2.0.0) |29 września, 2016 |30 sierpnia 2020 |
| [1.9.0](#1.9.0) |07 lipca 2016 |30 sierpnia 2020 |
| [1.8.0](#1.8.0) |14 czerwca 2016 |30 sierpnia 2020 |
| [1.7.0](#1.7.0) |26 kwietnia 2016 |30 sierpnia 2020 |
| [1.6.1](#1.6.1) |08 kwietnia, 2016 |30 sierpnia 2020 |
| [1.6.0](#1.6.0) |29 marca 2016 |30 sierpnia 2020 |
| [1.5.0](#1.5.0) |03, 2016 |30 sierpnia 2020 |
| [1.4.2](#1.4.2) |06, 2015 |30 sierpnia 2020 |
| 1.4.1 |06, 2015 |30 sierpnia 2020 |
| [1.2.0](#1.2.0) |06 sierpnia 2015 |30 sierpnia 2020 |
| [1.1.0](#1.1.0) |09 lipca 2015 |30 sierpnia 2020 |
| [1.0.1](#1.0.1) |25 maja 2015 |30 sierpnia 2020 |
| [1.0.0](#1.0.0) |07 kwietnia 2015 |30 sierpnia 2020 |
| 0.9.4 — przedlease |14 stycznia 2015 |29 lutego 2016 |
| 0.9.3 — przedlease |09, 2014 |29 lutego 2016 |
| 0.9.2 — przedlease |25 listopada 2014 |29 lutego 2016 |
| od 0.9.1 — przedlease |23 września, 2014 |29 lutego 2016 |
| 0.9.0 — przedlease |21 sierpnia 2014 |29 lutego 2016 |

## <a name="faq"></a>Najczęściej zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) . 

