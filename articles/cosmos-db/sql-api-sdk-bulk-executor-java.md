---
title: 'Azure Cosmos DB: zbiorczy interfejs API języka Java, zasoby & SDK'
description: Poznaj wszystkie informacje o interfejsie API i zestawie SDK środowiska wykonawczego zbiorczego, w tym o datach wydania, datach wycofania i zmianach między każdą wersją Azure Cosmos DB zbiorczego modułu wykonawczego Java SDK.
author: milismsft
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/21/2018
ms.author: adrianmi
ms.openlocfilehash: 0030f974a36dc80dc8c4112000aa5934126a2482
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836448"
---
# <a name="java-bulk-executor-library-download-information"></a>Biblioteka wykonawców zbiorczych języka Java: informacje o pobieraniu

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
|**Opis**|Biblioteka wykonawców zbiorczych umożliwia aplikacjom klienckim wykonywanie operacji zbiorczych w ramach kont Azure Cosmos DB. Biblioteka wykonawców zbiorczych udostępnia przestrzenie nazw BulkImport i BulkUpdate. Moduł BulkImport umożliwia zbiorcze pozyskiwanie dokumentów w sposób zoptymalizowany w taki sposób, że przepływność obsługiwana dla kolekcji jest używana w maksymalnym zakresie. Moduł BulkUpdate można zbiorczo aktualizować istniejące dane w kontenerach usługi Azure Cosmos jako poprawki.|
|**Pobieranie zestawu SDK**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Biblioteka modułu wykonującego zbiorczo w serwisie GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Dokumentacja interfejsu API**| [Dokumentacja interfejsu API języka Java](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Rozpoczęcie pracy**|[Wprowadzenie do zestawu SDK języka Java dla biblioteki wykonawczej](bulk-executor-java.md)|
|**Minimalne obsługiwane środowisko uruchomieniowe**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* Poprawka dla DocumentAnalyzer. Java w celu poprawnego wyodrębnienia wartości klucza zagnieżdżonej partycji z pliku JSON.

### <a name="294"></a><a name="2.9.4"/>2.9.4

* Dodaj funkcje w operacjach BulkDelete, aby ponowić próbę dotyczącą określonych błędów, a także zwrócić listę błędów użytkownika, które mogłyby zostać ponowione.

### <a name="293"></a><a name="2.9.3"/>2.9.3

* Aktualizacja zestawu Cosmos SDK w wersji 2.4.7.

### <a name="292"></a><a name="2.9.2"/>2.9.2

* Popraw wartość parametru "mergeAll", aby kontynuować "Identyfikator" i klucz partycji, tak aby wszystkie właściwości dokumentu z poprawkami, które są umieszczone po wartości "ID" i kluczu partycji, zostały dodane do listy zaktualizowanych elementów.

### <a name="291"></a><a name="2.9.1"/>2.9.1

* Zaktualizuj początkowy stopień współbieżności do 1 i dodano Dzienniki debugowania dla minibatch.


