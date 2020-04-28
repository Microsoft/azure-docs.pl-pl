---
title: 'Azure Cosmos DB: zbiorczy interfejs API platformy .NET, zasoby & SDK'
description: Poznaj wszystkie informacje o interfejsie API i zestawie SDK platformy .NET zbiorczego, w tym daty wydania, daty wycofania i zmiany między każdą wersją Azure Cosmos DB zbiorczego modułu wykonawczego .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169397"
---
# <a name="net-bulk-executor-library-download-information"></a>Biblioteka modułu wykonawczego platformy .NET: informacje o pobieraniu 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Źródło zmian platformy .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonawczy zbiorczej — Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Opis**| Biblioteka modułu wykonawczego platformy .NET umożliwia aplikacjom klienckim wykonywanie operacji zbiorczych na kontach Azure Cosmos DB. Ta biblioteka zawiera przestrzenie nazw BulkImport, BulkUpdate i BulkDelete. Moduł BulkImport umożliwia zbiorcze pozyskiwanie dokumentów w sposób zoptymalizowany w taki sposób, że przepływność obsługiwana dla kolekcji jest używana w maksymalnym zakresie. Moduł BulkUpdate można zbiorczo aktualizować istniejące dane w kontenerach usługi Azure Cosmos jako poprawki. Moduł BulkDelete umożliwia zbiorcze usuwanie dokumentów w sposób zoptymalizowany w taki sposób, że przepływność obsługiwana dla kolekcji jest używana w maksymalnym zakresie.|
|**Pobieranie zestawu SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Biblioteka modułu wykonującego zbiorczo w serwisie GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Wprowadzenie**|[Wprowadzenie do zestawu .NET SDK biblioteki wykonawczej zbiorczego](bulk-executor-dot-net.md)|
| **Bieżąca obsługiwana platforma**| Microsoft .NET Framework 4.5.2, 4.6.1 i .NET Standard 2,0 |

> [!NOTE]
> Jeśli korzystasz z modułu wykonawczego zbiorczego, zapoznaj się z najnowszą wersją 3. x [zestawu .NET SDK](tutorial-sql-api-dotnet-bulk-import.md), który zawiera zbiorczy moduł wykonawczy wbudowany w zestaw SDK. 

## <a name="release-notes"></a>Uwagi do wersji

### <a name="241-preview"></a><a name="2.4.1-preview"/>2.4.1 — wersja zapoznawcza

* Naprawiono TotalElapsedTime w odpowiedzi BulkDelete, aby prawidłowo zmierzyć łączny czas, łącznie z próbami.

### <a name="240-preview"></a><a name="2.4.0-preview"/>2.4.0 — wersja zapoznawcza

* Zmieniono zależność zestawu SDK na >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* Dodano obsługę zbiorczego wykonawcy wykresu do akceptowania czasu wygaśnięcia dla wierzchołków i krawędzi

### <a name="220-preview2"></a><a name="2.2.0-preview2"/>2.2.0 — preview2

* Rozwiązano problem, który spowodował wyjątek podczas elastycznego skalowania Azure Cosmos DB podczas uruchamiania w trybie bramy. Ta poprawka sprawia, że jest funkcjonalnie równoważna z wersją 1.4.1.

### <a name="210-preview2"></a><a name="2.1.0-preview2"/>2.1.0 — preview2

* Dodano obsługę BulkDelete dla kont interfejsu API SQL w celu akceptowania klucza partycji, spójnych kolekcji identyfikatorów dokumentów do usunięcia. Ta zmiana powoduje, że jest ona funkcjonalnie równoważna z wersją 1.4.0.

### <a name="200-preview2"></a><a name="2.0.0-preview2"/>2.0.0 — preview2

* W tym MongoBulkExecutor do obsługi .NET Standard 2,0. Ta funkcja sprawia, że jest ona funkcjonalnie równoważna z wersją 1.3.0, z dodaniem obsługi .NET Standard 2,0 jako platformy docelowej.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0 — wersja zapoznawcza

* Dodano .NET Standard 2,0 jako jedną z obsługiwanych platform docelowych, aby Biblioteka wykonawców zbiorczych działała z aplikacjami .NET Core.

### <a name="188"></a><a name="1.8.8"/>1.8.8

* Rozwiązano problem związany z MongoBulkExecutor, który zwiększa nieoczekiwany rozmiar dokumentu przez dodanie dopełnienia i w niektórych przypadkach, przechodząc do maksymalnego limitu rozmiaru dokumentu.

### <a name="187"></a><a name="1.8.7"/>1.8.7

* Rozwiązano problem z BulkDeleteAsync, gdy kolekcja zawiera zagnieżdżone ścieżki kluczy partycji.

### <a name="186"></a><a name="1.8.6"/>1.8.6

* MongoBulkExecutor teraz implementuje interfejs IDisposable i powinien zostać usunięty po użyciu.

### <a name="185"></a><a name="1.8.5"/>1.8.5

* Usunięto blokadę w wersji zestawu SDK. Pakiet jest teraz zależny od zestawu SDK >= 2.5.1.

### <a name="184"></a><a name="1.8.4"/>1.8.4

* Naprawiono obsługę identyfikatorów podczas wywoływania BulkImport z listą obiektów POCO z wartościami liczbowymi.

### <a name="183"></a><a name="1.8.3"/>1.8.3

* Naprawiono TotalElapsedTime w odpowiedzi BulkDelete, aby prawidłowo zmierzyć łączny czas, łącznie z próbami.

### <a name="182"></a><a name="1.8.2"/>1.8.2

* Stałe użycie procesora CPU w niektórych scenariuszach.
* Śledzenie używa teraz TraceSource. Użytkownicy mogą definiować detektory dla `BulkExecutorTrace` źródła.
* Rozwiązano rzadki scenariusz, który może spowodować blokadę podczas wysyłania dokumentów o rozmiarze zbliżonym do 2 MB.

### <a name="160"></a><a name="1.6.0"/>1.6.0

* Zaktualizowano moduł wykonujący zbiorczo, aby teraz używał najnowszej wersji zestawu SDK Azure Cosmos DB .NET (2.4.0)

### <a name="150"></a><a name="1.5.0"/>1.5.0

* Dodano obsługę zbiorczego wykonawcy wykresu do akceptowania czasu wygaśnięcia dla wierzchołków i krawędzi

### <a name="141"></a><a name="1.4.1"/>1.4.1

* Rozwiązano problem, który spowodował wyjątek podczas elastycznego skalowania Azure Cosmos DB podczas uruchamiania w trybie bramy.

### <a name="140"></a><a name="1.4.0"/>1.4.0

* Dodano obsługę BulkDelete dla kont interfejsu API SQL w celu akceptowania klucza partycji, spójnych kolekcji identyfikatorów dokumentów do usunięcia.

### <a name="130"></a><a name="1.3.0"/>1.3.0

* Rozwiązano problem, który spowodował problem z formatowaniem w agencie użytkownika używanym przez program wykonujący zbiorczo.

### <a name="120"></a><a name="1.2.0"/>1.2.0

* Ulepszono Importowanie i aktualizowanie interfejsów API do zbiorczego wykonawcy w celu nieprzezroczystego dostosowania do elastycznego skalowania kontenera Cosmos, gdy magazyn przekracza bieżącą pojemność bez zgłaszania wyjątków.

### <a name="112"></a><a name="1.1.2"/>1.1.2

* Niezależność od zestawu SDK platformy .NET DocumentDB do wersji 2.1.3.

### <a name="111"></a><a name="1.1.1"/>ppkt

* Rozwiązano problem, który spowodował wygenerowanie zbiorczego błędu JSRT podczas importowania do stałych kolekcji.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* Dodano obsługę operacji BulkDelete Azure Cosmos DB dla kont interfejsu API SQL.
* Dodano obsługę operacji BulkImport dla kont z interfejsem API Azure Cosmos DB dla MongoDB.
* DoDocumentDB zależności zestawu SDK platformy .NET do wersji 2.0.0. 

### <a name="102"></a><a name="1.0.2"/>1.0.2

* Dodano obsługę operacji BulkImport dla kont interfejsu API Gremlin Azure Cosmos DB.

### <a name="101"></a><a name="1.0.1"/>1.0.1

* Drobna poprawka błędu Azure Cosmos DB dla operacji BulkImport dla kont interfejsu API SQL.

### <a name="100"></a><a name="1.0.0"/>1.0.0

* Dodano obsługę operacji BulkImport i BulkUpdate dla kont interfejsu API SQL Azure Cosmos DB.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zbiorczej biblioteki środowiska wykonawczego w języku Java, zobacz następujący artykuł:

[Zestaw SDK biblioteki wykonawczej języka Java i informacje o wersji](sql-api-sdk-bulk-executor-java.md)
