---
title: Azure Cosmos DB Apache Spark 3 ŁĄCZNIK OLTP dla informacji o wersji i zasobów interfejsu API SQL (wersja zapoznawcza)
description: Dowiedz się więcej o łączniku oltp usługi Azure Cosmos DB Apache Spark 3 dla interfejsu API SQL (wersja zapoznawcza), w tym o datach wydaniach, datach wycofania i zmianach wprowadzonych między poszczególnymi wersjami zestawu SDK java języka SQL Azure Cosmos DB SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: e7d206b63d6e1bf741a5dc298dd5bbc2d48ab11b
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368640"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-preview-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark 3 ŁĄCZNIK OLTP dla podstawowego interfejsu API (SQL) (wersja zapoznawcza): informacje o wersji i zasoby
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

**Azure Cosmos DB OLTP platformy Spark 3 (wersja zapoznawcza)** Apache Spark w wersji 3 na Azure Cosmos DB przy użyciu interfejsu API SQL.
[Azure Cosmos DB](introduction.md) to globalnie dystrybuowana usługa bazy danych, która umożliwia deweloperom pracę z danymi przy użyciu różnych standardowych interfejsów API, takich jak SQL, MongoDB, Cassandra, Graph i Table.

> [!Note]
> Ta wersja łącznika OLTP Azure Cosmos DB Spark 3 jest kompilacją w wersji zapoznawczej.
> Ta kompilacja nie została przetestowana pod kątem obciążenia ani wydajności.
> Ta kompilacja nie jest zalecana do użycia w scenariuszach produkcyjnych.
>

## <a name="documentation"></a>Dokumentacja

- [Rozpoczęcie pracy](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
- [Interfejs API wykazu](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
- [Informacje o parametrach konfiguracji](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)


## <a name="version-compatibility"></a>Zgodność wersji

| Łącznik     | platforma Spark         | Minimalna wersja języka Java | Obsługiwane wersje scala |
| ------------- | ------------- | -------------------- | -----------------------  |
| 4.0.0-beta.1  | 3.1.1         |        8             | 2.12                     |

## <a name="download"></a>Pobierz

Możesz użyć współrzędnej maven pliku jar, aby automatycznie zainstalować łącznik platformy Spark na komputerze Databricks Runtime 8 z programu Maven: `com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.0.0-beta.1`

Możesz również zintegrować z Cosmos DB Spark w projekcie SBT:
```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.0.0-beta.1"
```

Cosmos DB Spark connector is available on Maven Central Repo (Łącznik Spark jest dostępny w [centralnym repo programu Maven).](https://search.maven.org/artifact/com.azure.cosmos.spark/azure-cosmos-spark_3-1_2-12/4.0.0-beta.1/jar)

### <a name="general"></a>Ogólne

Jeśli wystąpi jakakolwiek usterka, prosimy o zgłaszanie problemu [tutaj.](https://github.com/Azure/azure-sdk-for-java/issues/new)

Aby zasugerować nową funkcję lub zmiany, które można wprowadzić, należy wprowadzić problem w taki sam sposób, jak w przypadku usterki.

[!INCLUDE[Changelog](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos-spark_3-1_2-12/CHANGELOG.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [naszym przewodnikiem Szybki start, aby pracować z Azure Cosmos DB OLTP platformy Spark 3.](create-sql-api-spark.md)
