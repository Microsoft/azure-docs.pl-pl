---
title: 'Azure Cosmos DB: interfejs API java funkcji wykonywania zbiorczego, zestaw SDK & zasobów'
description: Dowiedz się wszystkiego o interfejsie API i zestawie SDK funkcji wykonywania zbiorczego Java, w tym o datach wydania, datach wycofania i zmianach wprowadzonych między poszczególnymi wersjami zestawu sdk java funkcji Azure Cosmos DB funkcji wykonywania zbiorczego.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 2d3c7026fd221b1a17b8efe56b03b2a26358c7ab
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364412"
---
# <a name="java-bulk-executor-library-download-information"></a>Biblioteka funkcji wykonywania zbiorczego Java: pobieranie informacji
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
|**Opis**|Biblioteka wykonywania zbiorczego umożliwia aplikacjom klienckim wykonywanie operacji zbiorczych Azure Cosmos DB kontach. Biblioteka funkcji wykonywania zbiorczego udostępnia przestrzenie nazw BulkImport i BulkUpdate. Moduł BulkImport umożliwia zbiorcze pobieranie dokumentów w sposób zoptymalizowany w taki sposób, aby przepływność aprowizowana dla kolekcji została zużyta w maksymalnym zakresie. Moduł BulkUpdate może zbiorczo aktualizować istniejące dane w kontenerach usługi Azure Cosmos jako poprawki.|
|**Zestaw SDK do pobrania**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Biblioteka wykonywania zbiorczego w usłudze GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Dokumentacja interfejsu API**| [Dokumentacja referencyjna interfejsu API języka Java](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Wprowadzenie**|[Wprowadzenie do biblioteki funkcji wykonywania zbiorczego zestawu Java SDK](bulk-executor-java.md)|
|**Minimalne obsługiwane środowisko uruchomieniowe**|[Zestaw Java Development Kit (JDK) 7+](/java/azure/jdk/)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Poprawka pliku DocumentAnalyzer.java w celu poprawnego wyodrębnienia zagnieżdżonych wartości klucza partycji z pliku JSON.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Dodaj funkcje w operacjach BulkDelete, aby ponowić próbę w przypadku określonych błędów, a także zwrócić użytkownikowi listę błędów, które można ponowić.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Aktualizacja zestawu Cosmos SDK w wersji 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Poprawka dla elementu "mergeAll" w celu kontynuowania na wartości "id" i klucza partycji tak, aby wszystkie poprawione właściwości dokumentu umieszczone po wartości "id" i klucz partycji zostały dodane do zaktualizowanej listy elementów.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Zaktualizuj stopień rozpoczęcia współbieżności na 1 i dodano dzienniki debugowania dla minibatch.