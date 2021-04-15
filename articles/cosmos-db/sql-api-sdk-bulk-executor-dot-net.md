---
title: 'Azure Cosmos DB: interfejs API .NET funkcji wykonywania zbiorczego, zestaw SDK & zasobów'
description: Dowiedz się wszystkiego o interfejsie API i zestawie SDK funkcji wykonywania zbiorczego platformy .NET, w tym o datach wydania, datach wycofania i zmianach wprowadzonych między poszczególnymi wersjami zestawu SDK funkcji Azure Cosmos DB funkcji wykonywania zbiorczego platformy .NET.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.openlocfilehash: 99b6e06b817f98e64968615b5e652b707268fe78
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364502"
---
# <a name="net-bulk-executor-library-download-information"></a>Biblioteka .NET dla wykonywania zbiorczego: pobieranie informacji 
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

| | Link/uwagi |
|---|---|
| **Opis**| Biblioteka wykonywania zbiorczego .NET umożliwia aplikacjom klienckim wykonywanie operacji zbiorczych Azure Cosmos DB kontach. Ta biblioteka zawiera przestrzenie nazw BulkImport, BulkUpdate i BulkDelete. Moduł BulkImport umożliwia zbiorcze pobieranie dokumentów w sposób zoptymalizowany w taki sposób, aby przepływność aprowizowana dla kolekcji została zużyta w maksymalnym zakresie. Moduł BulkUpdate może zbiorczo aktualizować istniejące dane w kontenerach usługi Azure Cosmos jako poprawki. Moduł BulkDelete umożliwia zbiorcze usuwanie dokumentów w sposób zoptymalizowany w taki sposób, aby przepływność aprowizowana dla kolekcji została zużyta w maksymalnym zakresie.|
|**Zestaw SDK do pobrania**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Biblioteka wykonywania zbiorczego w usłudze GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Dokumentacja interfejsu API**|[Dokumentacja referencyjna interfejsu API platformy .NET](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor)|
|**Wprowadzenie**|[Wprowadzenie do biblioteki wykonywania zbiorczego zestawu SDK platformy .NET](bulk-executor-dot-net.md)|
| **Bieżąca obsługiwana platforma**| Microsoft .NET Framework 4.5.2, 4.6.1 i .NET Standard 2.0 |

> [!NOTE]
> Jeśli używasz funkcji wykonywania zbiorczego, zapoznaj się z najnowszą wersją 3.x zestawu [.NET SDK,](tutorial-sql-api-dotnet-bulk-import.md)który ma wbudowaną w zestaw SDK wersję funkcji wykonywania zbiorczego. 

## <a name="release-notes"></a>Informacje o wersji

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1 —wersja zapoznawcza

* Naprawiono wartość TotalElapsedTime w odpowiedzi na błąd BulkDelete, aby poprawnie zmierzyć łączny czas, w tym wszystkie ponowne próby.

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0 —wersja zapoznawcza

* Zmieniono zależność zestawu SDK na >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-preview2

* Dodano obsługę zbiorczego wykonywania grafu w celu akceptowania czasu wygaśnięcia wierzchołków i krawędzi

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0-preview2

* Rozwiązano problem, który powodował wyjątki podczas elastycznego skalowania Azure Cosmos DB podczas uruchamiania w trybie bramy. Ta poprawka sprawia, że jest ona funkcjonalnie równoważna wersji 1.4.1.

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0-preview2

* Dodano obsługę funkcji BulkDelete dla kont interfejsu API SQL w celu akceptowania klucza partycji i krotek identyfikatorów dokumentów do usunięcia. Ta zmiana sprawia, że jest ona funkcjonalnie równoważna wersji 1.4.0.

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0-preview2

* W tym mongoBulkExecutor do obsługi .NET Standard 2.0. Ta funkcja sprawia, że jest funkcjonalnie równoważna wersji 1.3.0, z dodaniem obsługi .NET Standard 2.0 jako struktury docelowej.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-preview

* Dodano .NET Standard 2.0 jako jedną z obsługiwanych platform docelowych, aby biblioteka wykonywania zbiorczego działała z aplikacjami .NET Core.

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* Rozwiązano problem z elementem BulkDeleteAsync, który dotyczył sytuacji, w których wartości z cudzysłowami ucieczki były przekazywane jako parametry wejściowe.

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* Rozwiązano problem w programie MongoBulkExecutor, który nieoczekiwanie zwiększał rozmiar dokumentu przez dodanie dopełnienia i w niektórych przypadkach przekroczono maksymalny limit rozmiaru dokumentu.

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* Rozwiązano problem z elementem BulkDeleteAsync, który dotyczył zagnieżdżonych ścieżek kluczy partycji w kolekcji.

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* MongoBulkExecutor implementuje teraz IDisposable i oczekuje się, że zostanie usunięty po jego zakończeniu.

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* Usunięto blokadę wersji zestawu SDK. Pakiet jest teraz zależny od zestawu SDK >= 2.5.1.

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* Naprawiono obsługę identyfikatorów podczas wywoływania funkcji BulkImport z listą obiektów POCO z wartościami liczbymi.

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* Naprawiono wartość TotalElapsedTime w odpowiedzi na błąd BulkDelete, aby poprawnie zmierzyć łączny czas, w tym wszystkie ponowne próby.

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* Naprawiono wysokie użycie procesora CPU w niektórych scenariuszach.
* Śledzenie używa teraz tracesource. Użytkownicy mogą definiować odbiorniki dla `BulkExecutorTrace` źródła.
* Rozwiązano rzadki scenariusz, który mógł powodować blokadę podczas wysyłania dokumentów o rozmiarze niemal 2 MB.

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* Zaktualizowano zbiorczy wykonawcę, aby teraz używać najnowszej wersji zestawu SDK platformy .NET Azure Cosmos DB (2.4.0)

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* Dodano obsługę zbiorczego wykonywania grafu w celu akceptowania czasu wygaśnięcia wierzchołków i krawędzi

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* Rozwiązano problem, który powodował wyjątki podczas elastycznego skalowania Azure Cosmos DB podczas pracy w trybie bramy.

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* Dodano obsługę funkcji BulkDelete dla kont interfejsu API SQL w celu akceptowania klucza partycji i krotek identyfikatorów dokumentów do usunięcia.

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* Rozwiązano problem, który powodował problem z formatowaniem w agencie użytkownika używanym przez wykonawcę zbiorczego.

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* Ulepszyliśmy import funkcji wykonywania zbiorczego i zaktualizowaliśmy interfejsy API w celu przezroczystego dostosowania do elastycznego skalowania kontenera usługi Cosmos, gdy magazyn przekracza bieżącą pojemność bez zgłaszania wyjątków.

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* Przesuniliśmy zależność zestawu SDK .NET usługi DocumentDB do wersji 2.1.3.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Rozwiązano problem, który powodował, że wykonywanie zbiorcze powodowało zgłaszanie błędu JSRT podczas importowania do stałych kolekcji.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Dodano obsługę operacji BulkDelete dla Azure Cosmos DB interfejsu API SQL.
* Dodano obsługę operacji BulkImport dla kont Azure Cosmos DB interfejsu API usługi MongoDB.
* Wznoszona zależność zestawu SDK platformy .NET usługi DocumentDB do wersji 2.0.0. 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* Dodano obsługę operacji BulkImport dla Azure Cosmos DB gremlin API.

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* Drobna poprawka usterki operacji BulkImport dla Azure Cosmos DB interfejsu API SQL.

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Dodano obsługę operacji BulkImport i BulkUpdate dla Azure Cosmos DB interfejsu API SQL.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o bibliotece java funkcji wykonywania zbiorczego, zobacz następujący artykuł:

[Zestaw SDK biblioteki funkcji wykonywania zbiorczego Java i informacje o wersji](sql-api-sdk-bulk-executor-java.md)