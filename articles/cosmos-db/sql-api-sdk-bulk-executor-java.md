---
title: 'Azure Cosmos DB: zbiorczy interfejs API języka Java, zasoby & SDK'
description: Poznaj wszystkie informacje o interfejsie API i zestawie SDK środowiska wykonawczego zbiorczego, w tym o datach wydania, datach wycofania i zmianach między każdą wersją Azure Cosmos DB zbiorczego modułu wykonawczego Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 72a224d4ad1807d095baa8db819878bf2e22e133
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802722"
---
# <a name="java-bulk-executor-library-download-information"></a>Biblioteka wykonawców zbiorczych języka Java: informacje o pobieraniu

> [!div class="op_single_selector"]
> * [Zestaw SDK .NET w wersji 3](sql-api-sdk-dotnet-standard.md)
> * [Zestaw .NET SDK w wersji 2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK 2](sql-api-sdk-dotnet-core.md)
> * [Zestaw SDK zmiany źródła .NET w wersji 2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK 4](sql-api-sdk-java-v4.md)
> * [Async Java SDK 2](sql-api-sdk-async-java.md)
> * [Sync Java SDK 2](sql-api-sdk-java.md)
> * [Wiosenne dane v2](sql-api-sdk-java-spring-v2.md)
> * [Wiosenne dane v3](sql-api-sdk-java-spring-v3.md)
> * [Łącznik platformy Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy — .NET V2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonawczy zbiorczej — Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Opis**|Biblioteka wykonawców zbiorczych umożliwia aplikacjom klienckim wykonywanie operacji zbiorczych w ramach kont Azure Cosmos DB. Biblioteka wykonawców zbiorczych udostępnia przestrzenie nazw BulkImport i BulkUpdate. Moduł BulkImport umożliwia zbiorcze pozyskiwanie dokumentów w sposób zoptymalizowany w taki sposób, że przepływność obsługiwana dla kolekcji jest używana w maksymalnym zakresie. Moduł BulkUpdate można zbiorczo aktualizować istniejące dane w kontenerach usługi Azure Cosmos jako poprawki.|
|**Pobieranie zestawu SDK**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Biblioteka modułu wykonującego zbiorczo w serwisie GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Dokumentacja interfejsu API**| [Dokumentacja interfejsu API języka Java](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Wprowadzenie**|[Wprowadzenie do zestawu SDK języka Java dla biblioteki wykonawczej](bulk-executor-java.md)|
|**Minimalne obsługiwane środowisko uruchomieniowe**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Poprawka dla DocumentAnalyzer. Java w celu poprawnego wyodrębnienia wartości klucza zagnieżdżonej partycji z pliku JSON.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Dodaj funkcje w operacjach BulkDelete, aby ponowić próbę dotyczącą określonych błędów, a także zwrócić listę błędów użytkownika, które mogłyby zostać ponowione.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Aktualizacja zestawu Cosmos SDK w wersji 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Popraw wartość parametru "mergeAll", aby kontynuować "Identyfikator" i klucz partycji, tak aby wszystkie właściwości dokumentu z poprawkami, które są umieszczone po wartości "ID" i kluczu partycji, zostały dodane do listy zaktualizowanych elementów.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Zaktualizuj początkowy stopień współbieżności do 1 i dodano Dzienniki debugowania dla minibatch.


