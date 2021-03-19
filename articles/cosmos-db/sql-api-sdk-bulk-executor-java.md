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
ms.openlocfilehash: 5ef75719686f9299fee56cad247ca49167171813
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577176"
---
# <a name="java-bulk-executor-library-download-information"></a>Biblioteka wykonawców zbiorczych języka Java: informacje o pobieraniu
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

| | Link/notatki |
|---|---|
|**Opis**|Biblioteka wykonawców zbiorczych umożliwia aplikacjom klienckim wykonywanie operacji zbiorczych w ramach kont Azure Cosmos DB. Biblioteka wykonawców zbiorczych udostępnia przestrzenie nazw BulkImport i BulkUpdate. Moduł BulkImport umożliwia zbiorcze pozyskiwanie dokumentów w sposób zoptymalizowany w taki sposób, że przepływność obsługiwana dla kolekcji jest używana w maksymalnym zakresie. Moduł BulkUpdate można zbiorczo aktualizować istniejące dane w kontenerach usługi Azure Cosmos jako poprawki.|
|**Zestaw SDK do pobrania**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Biblioteka modułu wykonującego zbiorczo w serwisie GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Dokumentacja interfejsu API**| [Dokumentacja interfejsu API języka Java](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Wprowadzenie**|[Wprowadzenie do zestawu SDK języka Java dla biblioteki wykonawczej](bulk-executor-java.md)|
|**Minimalne obsługiwane środowisko uruchomieniowe**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/)|

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